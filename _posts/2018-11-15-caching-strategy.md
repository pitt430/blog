---
layout: post
title: Cache Strategy for high concurrent requests
date: 2019-06-02
Author: peterli
tags: [cache, redis]
comments: true
toc: true
pinned: true
---

## Introduction

The other day, one of my friend's new app had a serious problem. A bunch of people were to open the app ready for an important event which triggered the fuse.

I talked about the situation with relevant people and had a deep investigation about it, which led to this article.

### Why cache?

Why do we mention caching so often when we have high concurrency requests? The most reason is that current disk and network IO are hundreds of times worse than memory IO.

Do a simple calculation, if we need some data, the data read from the database disk needs 0.1 s, from the switch to 0.05 s, then each request to complete a minimum of 0.15 s (of course, in fact, disk and network IO nor so slowly, and here are just examples), the database server can only respond to 67 requests per second. If this data exists in native memory and takes only 10us to read, it will respond to 100,000 requests per second.

By keeping high-frequency data closer to the CPU to reduce data transfer time and thus improve processing efficiency, this is what caching is all about.

### Where to use the cache?

Everywhere. Such as:

When we read data from the hard disk, the operating system also reads nearby data into memory

For example, when the CPU reads data from memory, it also reads additional data into its cache

Instead of byte by byte, a batch of data is sent and received in a buffer between input and output

This is at the system level, and at the software system design level, a lot of caching is also used:

The browser caches the elements of the page so that it avoids having to download data (such as large images) from the Internet when visiting the page over and over again

Web services deploy static things on CDN ahead of time, which is also a kind of cache

The database caches queries, so the second query is faster than the first

An in-memory database (such as redis) chooses to store large amounts of data in memory rather than on a hard disk, which can be considered a large cache, but only to cache the entire database

The application keeps the results of the last few calculations in local memory, and if the next request is still the original request, it skips the calculation and returns the results

### Accident analysis

Going back to the beginning of this article, how is the system designed? The bottom layer is the database, and a layer of redis is put in the middle. All the data needed by the previous business system are directly taken from redis, and then the calculated results are returned to the app. The synchronization of database and redis is also guaranteed by the program to avoid redis penetration and prevent a large number of requests in the program from redis can not be found, so it is a swarm of to check the database, directly crush the database situation. From this point of view, this step is actually ok.

But the system has two problems:

1. Although all the data needed by the business system are in redis, they are stored separately. What does that mean? If I make a request from the foreground, then I go to redis to get the title, then I get the author, then I get the content, then I get the comment, then I get the forwarding number and so on... As a result, the foreground requests once, and the background requests redis more than ten times. When high concurrency occurs, the pressure will be magnified by more than ten times, and redis response and network response will inevitably slow down.

2. In fact, the business people also realized that this situation could happen, so they made a circuit breaker mechanism and set up a cache pool to store some spare data. If the main business time-outs, they can directly fetch data from the cache pool and return it. However, they did not think through the design. The data expiration time of this alternative pool was too long, and there were even data updated three days ago in it. Finally, a large number of users brushed out the small video of the wild ecology three days ago...
   Speaking of which, I wonder if readers are aware of one of their most fatal problems: the business system does not consider local caching (that is, caching in business server memory) at all. For example, once a large number of users flood in at the same time, they must rush to a few contents. This kind of highly concentrated and highly frequent data access, which does not need to be customized for every user, is just like writing "please cache me" on their faces.
   At this point, a layer of local caching on the business side, directly storing the calculated data locally, would greatly reduce the pressure on the network and redis, without triggering a fuse on the spot.

## Talk about the cache pits

Caching is useful, but it can also create a lot of holes if you don't use it well:

The cache to penetrate

Cache penetration means that a request is received, but it is not in the cache, so it can only be looked up in the database and put into the cache. There are two risks. One is that there are many requests to access the same data at the same time, and then the business system sends all these requests to the database. The second is that someone maliciously constructs a piece of data that does not logically exist, and then sends a large number of requests, so that each request is sent to the database, possibly causing the data to die.

How do you deal with this situation? For malicious access, one idea is to check in advance, the malicious data directly filtered out, do not send to the database layer; The second idea is the cache empty result, which is to record a piece of data that does not exist in the cache for the query, which can effectively reduce the number of times of querying the database.

What about non-malicious access? This is done in conjunction with cache breakdown.

### Cache breakdown

None of the data mentioned above, and then many requests are sent to the database can be classified as cache breakdown: for hot data, when the data fails, all the requests are sent to the database to request the update cache, and the database is overwhelmed.

How do you prevent this? One idea is a global lock, where all requests for access to a particular piece of data share a lock, so that the one that gets the lock is eligible to access the database, and other threads have to wait. But now the business is distributed, local locks can't control other servers waiting, so global locks are used, such as redis setnx to implement global locks.

Another idea is to actively refresh data that is about to expire, which can be done in many ways, such as starting a thread to poll data, dividing all data into different cache intervals, refreshing data between partitions periodically, and so on. This second idea has to do with the cache avalanche that we're going to talk about.

### Cache avalanche

A cache avalanche is when we set the same expiration time for all of our data, and then at some historical point, the entire cache expires, and then all of a sudden all of the requests get sent to the database, and the database crashes.

The solution is either divide and conquer, divide smaller cache intervals, and expire by interval; Or we can add a random value to the expiration time of each key, so as to avoid expiration at the same time and achieve the purpose of mispeak refreshing cache.

### Cache refresh

When it comes to flushing the cache, there are some pitfalls. For example, in one of my previous jobs, there was a big campaign. It was in full swing, and all the advertising space suddenly went blank. Later, I found out why. All the advertising materials were in the cache, and then a program was set up to refresh the cache, each time refreshing the current material in full.

The bad is in this whole quantity. Because the flow of large activities, advertising update pressure is also very large, responsible for providing the program to update the material collapse. The program that refreshes the cache receives Null as a result of the request. And then it's nice to see that the refresh program emptying the entire cache based on this null, and all the AD material is dead.

In short, caching for high-concurrency systems requires careful design, taking into account all the edges and corners, and any minor oversight can lead to system crashes.

Finally, welcome to follow my public account, to exchange technology, or career?
