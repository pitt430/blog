---
layout: post
title: Performance optimization
date: 2019-07-15
Author: peterli
tags: [performance, sql]
comments: true
toc: true
pinned: true
---

## Introduction

Recently, a new version of the system was released. During beta customer testing, a lot of problems were exposed, except for some business and exception problems, which focused on performance. I'm lucky enough to have access to these performance tuning opportunities, but it's certainly time to learn more.

Performance optimization is a perennial problem, with typical performance problems such as slow page response, interface timeout, high server load, low concurrency, frequent database deadlocks, etc. There are many performance problems, such as disk I/O, memory, networks, algorithms, large data volumes, and so on. We can roughly divide the performance problem into four levels: code level, database level, algorithm level, and architecture level.

So I'm going to share with you the tools, methods, and techniques of performance tuning based on actual performance optimization cases.

## Mindset

When it comes to performance problems, you may feel headache, because generally performance problems are relatively urgent, ranging from affecting customer experience to financial loss caused by downtime, and performance problems are relatively subtle and difficult to find. So we don't know what to do for a while, and then it's easy to reject it from the bottom of our hearts and not want to take the hot potato.

As it happens, performance tuning is an important indicator of a programmer's level.

Because dealing with bugs, crashes, tuning, intrusions, and other unexpected events is more important than programming itself. When faced with an unknown problem, how to locate the core problem under complex conditions, how to analyze the potential causes of the problem, how to eliminate interference and restore a minimum verifiable scenario, how to seize the key data to verify their own guesses and experiments, are all the best scenarios that reflect the programmer's thinking ability. Yes, thinking is more important than experience in measuring the ideal programmer.

So if you're not comfortable with mediocrity, embrace every opportunity for performance tuning. When you have the right mindset, half of your performance problems are solved.

## Skills

When you get a performance problem, don't rush to the tool first, understand the context and severity of the problem first. Then roughly based on their own experience to make estimates. For example, the customer came to a performance problem and said that the system was down, which has caused a loss of funds. When it comes to money, we are all very sensitive and decide whether to accept the challenge or not according to our own level. This is not escape, but self-knowledge.

Once you understand the context of the problem, the next step is to try to reproduce the problem. If you can reproduce the problem in the test environment, then it's easy to trace the problem. Stable if the problem cannot be reproduced or can only in a production environment, the problem is relatively difficult, then collect the scene immediately to the evidence, including but not limited to, grasp the dump, collect the application and system log, pay attention to the CPU memory, database backup and so on, might as well try again again, after such as restoring the customer database to test environment.

Whether or not the problem recurs, the next step is to roughly classify the problem as a code-level business logic problem, a database-level operational time consuming problem, or a system architecture throughput problem. So how do you know for sure? I prefer to start with the database. My usual practice is to use database monitoring tools to first track the Sql time consumption. If you're monitoring long running SQL statements, it's basically a database level problem, otherwise it's a code level problem. If it is code level, then after studying the code, it will be refined into algorithm or architecture level problem.

Once you've identified the type of problem, it's time to use tools to pinpoint the problem:

- Sql time consuming issues, the use of the free Plan Explorer is recommended to analyze the execution Plan.

- NET Developer Bundle, RedGate's Performance Analysis suite. Then there are dotTrace --.net performance profiler, dotMemory--.net memory profiler, Jet Brains. Then there's the anti-human Windbg; And so on.

After accurately locating the problem points, it is time to optimize. Believe to this step, is the choice of optimization strategy, here will not expand.

After optimization, of course, the final test, after all, how much optimization, we also have to do in mind.

The above is a little too tedious, below we directly on the case.

## Problem solving sceniros

### SQL performance tuning

### Code performance tunning

> Batch insert 1000 orders, take 10 mins

After getting this problem, after local replay, monitoring SQL time consumption without exception, then focus on analyzing the code. Because of the complexity of the business logic for volume validation, it's hard to avoid the 3,500 + lines of code and the odd comment that comes with implementing it directly in a class file. Escape is not a way, or tool analysis.

This time I chose the Performance Profiler that comes with time VS, an extremely powerful Performance tuning tool in the development environment. For our current case, we just need to track the DLL corresponding to the specified service. The steps are as follows:

1. Analyze - > Profiler - > New Performance Session

2. Open Performance Explorer

3. Find the newly added Performance Session, right-click Targets, and then select Add Target Binary to Add the DLL file to be tracked

4. Run the app

5. Select the Performance Session and right-click the Attach corresponding process to track Performance

6. During the tracking process, the tracking can be suspended and stopped at any time

![perfprofiler]

[perfprofiler]: https://raw.githubusercontent.com/pitt430/blog/master/images/perfoptimization/codeanalysis.png "Logo Title Text 2"

### Algorithm tunning

## Conclusion

Performance tuning is a gradual process, and cannot be accomplished overnight. The selection and use of tools is not covered in this article, and I hope you don't get hung up on it. When you really want to solve a problem, trust the use of tools can't help you.

- Adjust your attitude and respond positively

- Understand the performance context, gather evidence, and try to reproduce it

- Problem classification: first monitor SQL time consumption to determine if it is SQL or code level

- Use performance analysis tools to identify problem areas

- Tuning test
