---
layout: post
title: IdentityServer4
date: 2019-03-20
Author: peterli
tags: [identity, OAuth]
comments: true
toc: true
pinned: true
---

## Introduction

Now application development emerge in endlessly, based on the browser's web application, based on Windows desktop application and UWP application and so on, so many kinds of applications, creates challenges for application development, we need to consider implementation of each applicaiton , but also we need to consider the interaction between the individual application, extracting common module, `identity authentication and authorization` is an essential part of each application. As information security requirements are very demanding, so a standard set of identity authentication and authorization framework is crucial.
![idserver]

[idserver]: https://raw.githubusercontent.com/pitt430/blog/master/images/identity/intro.png "Logo Title Text 2"

> IdentityServer4 is such a framework that implement OpenId Connect and OAuth2.0 for ASP.NET CORE

## OAuth 2.0 & OpenId Connect

- OpenId Connect && Oauth2.0
  - OpenId -> Authentication
  - OAuth2.0 -> Authorization
  - OIDC : OpenID Connect -> (Identity, Authentication) + Oauth2.0
- Features
  - **Authentication as a service**: Centralized login logic and workflow for all of your applications (web, native, mobile, services).
  - **Access Control for API**: Issue access tokens for APIs for various types of clients, e.g. server to server, web applications, SPAs and native/mobile apps.
  - **Federation Gateway**: Support for external identity providers like Azure Active Directory, Google, Facebook etc. This shields your applications from the details of how to connect to these external providers.
  - **Focus on Customization**: The most important part - many aspects of IdentityServer can be customized to fit your needs. A framework not a boxed product which allow you to code for your scenarios.
- Ploblem Solved
  - **Single Sing On/Out**: single sign on over multiple applicaiton types.
  - **Centralized Authentication and Authorization**
  - **Support Web , Native, Mobile, Service**

### OpenID

> OpenID allows you to use an existing account to sign in to multiple websites, without needing to create new passwords.

### OAuth2.0

> An open protocol to allow secure authorization in a simple and standard method from web, mobile and desktop applications.
> OAuth allows users to provide a token instead of a username and password to access their data stored on a particular service. Each token authorizes access to a particular resource within a particular site (for example, just video in a particular album). In this way, OAuth allows users to authorize third-party web sites to access some specific information they store with another service provider, rather than all content.

### OpenId Connect

> An authentication layer built on top of Oauth 2.0, include Authenticaiton and Authorizaiton

## Terms

1. User
2. Client
3. Resource: Identity Data, APi
4. Identity Server: Authentication & Authorization Server
5. Token: Access Token and Identity Token

## JWT Bearer Authentication

### HTTP Authentication Flow

> HTTP provides a standard authentication framework: the server can challenge a client's request, against which the client provides authentication credentials. The workflow for requests and response follows: the server returns an 401 (Unauthorized) status code to the client, and adds information about how to Authenticate in the www-authenticate header that includes at least one way to Authenticate. The client can then add Authorization header in the request for Authentication.

Bearer authentication (also known as Token authentication) is an HTTP protocol using Bearer tokens that contain a security Token called an Bearer Token. So the core of Bearer authentication is Token. How to ensure Token security is a top priority. One way is to use Https, and the other is to encrypt the Token signature. JWT is a popular Token encoding method.

### JWT

JWT has three parts:

1. Header: `alg` and `typ`, alg means algorithm, typ is type of token. This part use Base64Url
2. Payload: is to save information, include any kind of claims, alos use BaseURL
3. Signature: signed using server side key and ensure the Token is not tampered

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```
