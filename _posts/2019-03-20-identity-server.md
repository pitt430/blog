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

## Authorization Flows

Oauth2.0 has four authorizaiton flows:

1. Implicit: simplified flow; Acquire token through browser redirection.
2. Client Credentials:
3. Resource Owner Password Credentials:
4. Authorization Code

### Client Credential

The Client credential pattern is the simplest authorization pattern because the authorization process occurs only between Client and Identity Server.

The scenario for this pattern is server-to-server communication. For example, for an e-commerce website, the order and logistics system are separated into two services for deployment. The order system needs to access the logistics system to track the logistics information, and the logistics system needs to access the tracking number information of the order system to refresh the logistics information regularly. Authorization of services between the two systems can be achieved through this pattern

### Resource Owner Password Credentials

The Resource Owner is actually a User. The password mode has one more participant than the client credential mode, which is User. Request to Identity Server for access token through User's username and password. This mode requires clients not to store passwords. However, we cannot be sure that the client has stored the password, so this pattern only applies to trusted clients. Otherwise, the risk of password leakage will occur. This pattern is not recommended.

### Authorization Code

Authorization code mode is a hybrid mode, is currently the most complete function, the most rigorous process of authorization mode. It is divided into two major steps: authentication and authorization.

The process is as follows:

1. The user accesses the client, which directs the user to Identity Server.

2. The user fills in the login information to authorize the client, and the identity server redirects the URI specified by the client and returns a Authorization Code to the client.

3. The client ask Identity Server for Access Token by Authorization Code.

### Implicit

This simplified mode is relative to the Authorization Code mode, it no longer require [Client] , all the authentication and authorization are through the browser.

## IdentityServer 4 Integration

Through sorting out the above points, we have a general understanding of some related concepts of OpenId Connect and OAuth 2.0.
IdentityServer4 is an authentication middleware customized for ASP.NET CORE, which implements OpenId Connect and OAuth 2.0 protocols.
THen we can discuss how to integrate IdentityServer4 into your system.

1. How to configure and start IdentityServer middleware.
2. How do resources configure and enable the authentication and authorization middleware.
3. How does Client authenticate and authorize.

### Configure and Start

- Configure the protected resource list
- Configure allowed authenticated Client

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        // configure identity server with in-memory stores, keys, clients and scopes
        services.AddIdentityServer()
            .AddDeveloperSigningCredential()
             //Identity Resource
            .AddInMemoryIdentityResources(Config.GetIdentityResources())
              //API resource
            .AddInMemoryApiResources(Config.GetApiResources())
             //Configure allowed clients
            .AddInMemoryClients(Config.GetClients())
            .AddTestUsers(Config.GetUsers());
        services.AddAuthentication()
              //Add Google authentication(Optional)
            .AddGoogle("Google", options =>
            {
                options.SignInScheme = IdentityServerConstants.ExternalCookieAuthenticationScheme;
                options.ClientId = "434483408261-55tc8n0cs4ff1fe21ea8df2o443v2iuc.apps.googleusercontent.com";
                options.ClientSecret = "3gcoTrEDPPJ0ukn_aYYT6PWo";
            })
            //Add other identity server provider.
            .AddOpenIdConnect("oidc", "OpenID Connect", options =>
            {
                options.SignInScheme = IdentityServerConstants.ExternalCookieAuthenticationScheme;
                options.SignOutScheme = IdentityServerConstants.SignoutScheme;
                options.Authority = "https://demo.identityserver.io/";
                options.ClientId = "implicit";
                options.TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name",
                    RoleClaimType = "role"
                };
            });
    }
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }
        //Add identity server middleware to pipeline
        app.UseIdentityServer();
        app.UseStaticFiles();
        app.UseMvcWithDefaultRoute();
    }
```

### Resource Protection Configuration

After configuring the Identity Server, we need to protect the resources, and how to direct all the authentication and authorization to identity server.
The flow of Client access Resource:

1. Client ask for resource, If Resource need authentication and authorization , direct the request to identity server.
2. Identity Server return the Token according to Client's authorization type.
3. Client can verify the correcteness of Token.

Code

```csharp
[Route("[controller]")]
[Authorize]
public class IdentityController : ControllerBase
{
    [HttpGet]
    public IActionResult Get()
    {
        return new JsonResult(from c in User.Claims select new { c.Type, c.Value });
    }
}
```

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvcCore()
            .AddAuthorization()
            .AddJsonFormatters();
        //Set authentication type
        services.AddAuthentication("Bearer")
              //add token authentication to DI
            .AddIdentityServerAuthentication(options =>
            {
                //define authoriztion URL
                options.Authority = "http://localhost:5000";
                options.RequireHttpsMetadata = false;
                options.ApiName = "api1";
            });
    }
    public void Configure(IApplicationBuilder app)
    {
        //Add middleware to pipeline
        app.UseAuthentication();
        app.UseMvc();
    }
}
```

### Configure Client Request

- Use DiscoverClient to discover the Token Endpoint
- Use TokenClient To apply access token
- Use HttpClient to access API

```csharp
// discover endpoints from metadata
var disco = await DiscoveryClient.GetAsync("http://localhost:5000");
// request token（ClientCredentials type）
var tokenClient = new TokenClient(disco.TokenEndpoint, "client", "secret");
var tokenResponse = await tokenClient.RequestClientCredentialsAsync("api1")
if (tokenResponse.IsError)
{
    Console.WriteLine(tokenResponse.Error);
    return;
}
Console.WriteLine(tokenResponse.Json);
Console.WriteLine("\n\n");
// call api
var client = new HttpClient();
client.SetBearerToken(tokenResponse.AccessToken);
```

For the ASP.NET Web console client, let's first answer a question:

Does the Web application need to log in?

1. If you need to log in, you need to authenticate.
2. After successful authentication, session state maintenance is required.

After answering the above questions, we have sorted out the configuration points:

1. Add the authentication middleware
2. Cookies are enabled for session retention
3. Add OIDC, using the authentication service provided by our own IdentityServer

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Clear();
    services.AddAuthentication(options =>
        {
            options.DefaultScheme = "Cookies";
            options.DefaultChallengeScheme = "oidc";
        })
        .AddCookie("Cookies")
        .AddOpenIdConnect("oidc", options =>
        {
            options.SignInScheme = "Cookies";
            options.Authority = "http://localhost:5000";
            options.RequireHttpsMetadata = false;
            options.ClientId = "mvc";
            options.SaveTokens = true;
        });
}
public void Configure(IApplicationBuilder app, IHostingEnvironment env
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }
    app.UseAuthentication();
    app.UseStaticFiles();
    app.UseMvcWithDefaultRoute();
}
```
