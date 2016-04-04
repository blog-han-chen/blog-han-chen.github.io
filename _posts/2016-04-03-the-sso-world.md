---
layout: post
title: "The Single Sign On World"
description: "Explains why Single Sign On is valuable, what some of the solutions are and their pros and cons"
category: technology
tags: []
---
{% include JB/setup %}


We all used Single Sign On
------
When you login to stackoverflow.com using Facebook, you are using Single Sign On (SSO for short). 
There is nothing really exciting going on from an end user point of view, all it means is that you have to remember one fewer password. But from a developer's standpoint, it is a beautiful solution to many problems. 


Why SSO? 
------
SSO solves 2 problems: **Platformization** and **Identity Management**. 


Take Facebook as an example, many applications are built around it without having to maintain their own user database: they use Facebook as an _authorization server/identity provider_. Without SSO, Facebook cannot become a platform for other third party services. 

At a company (especially tech firms), it is common that employees have access to multiple software services. E.g. Gmail, Dropbox, etc.. If each employee needs to register an account for each application, that is (# of employee X # applications) accounts to manage. It is difficult to track who has access to what. Also, when an employee quits, the IT department has to revoke access to all those applications. It becomes more tedious as the company size grows. 

The solution is to use SSO. In this case, the company itself becomes the _identity provider_ and services like Dropbox, Gmail, BambooHR simply asks the company for identity of the user. the IT department now has controll over creating, tracking, changing and revoking access on a per user, per application basis. Much more powerful and easier. 


Existing SSO technologies
------

There are two types of SSO specifications **Security Assertion Markup Language (SAML)** and **OAuth2.0**.

The following give overview for each.(text explanation and photo stolen from [this blog](https://www.mutuallyhuman.com/blog/2013/05/09/choosing-an-sso-strategy-saml-vs-oauth2/))

{% include image.html
            img="image/the-sso-world/saml2-flow.png"
            title="SAML Web Flow"
            caption="" 
 %}

A - a user opens their web-browser and goes to MyPhotos.com which stores all of their photos. MyPhotos.com doesn't handle authentication itself.

B - to authenticate the user MyPhotos.com constructs a SAML Authnrequest, signs it, optionally encrypts it, and encodes it. After which, it redirects the user's web browser to the Identidy Provider (IdP) in order to authenticate. The IdP receives the request, decodes it, decrypts it if necessary, and verifies the signature.

C - With a valid Authnrequest the IdP will present the user with a login form in which they can enter their username and password.

D- Once the user has logged in, the IdP generates a SAML token that includes identity information about the user (such as their username, email, etc). The Id takes the SAML token and redirects the user back to the Service Provider (MyPhotos.com).

E - MyPhotos.com verifies the SAML token, decrypts it if necessary, and extracts out identity information about the user, such as who they are and what their permissions might be. MyPhotos.com now logs the user into its system, presumably with some kind of cookie and session.

>Key Highlights:
>Client receives SAML after entering username and password
>SAML is a long xml document, as opposed to a lightweight token


{% include image.html
            img="image/the-sso-world/oauth2-flow.png"
            title="OAuth2 Web Flow"
            caption="" 
%}

* A - a user opens their web-browser and goes to MyPhotos.com which stores all of their photos. MyPhotos.com doesn't handle authentication itself, so the user is redirected to the Authorization Server with a request for authorization. The user is presented with a login form and is asked if they want to approve the Resource Server (MyPhotos.com) to act on their behalf. The user logs in and they are redirected back to MyPhotos.com.

* B - the client [at authorization side] receives an authorization grant code as a part of the redirect and then passes this along to the client [from MyPhotos.com].

* C - the Client [from MyPhotos.com] then uses that authorization grant code to request an access token from the Authorization Server.

* D - if the authorization grant code is valid, then the Authorization Server grants an access token. The access token is then used by the client to request resources from the Resource Server (MyPhotos.com).

* E - MyPhotos.com receives the request for a resource and it receives the access token. In order to make sure it's a valid access token it sends the token directly to the Authorization Server to validate. If valid, the Authorization Server sends back information about the user.

* F - having validated the user's request MyPhotos.com sends the requested resource back to the user.

> Key Highlights: 
> The client must request for access token separately, after entering username + password
> MyPhotos.com server must make another trip to Authorization service to retrieve identity info


Pros and Cons
-------



At a high level, SAML is less chatty and has been a popular standard for web apps. Because SAML file contains all identity information about the user, after client receives SAML Response from the Identity Provider, it is considered authenticated and can access services from service provider until SAML expires. In this way, SAML file is similar to JWT. 

On the other hand, OAuth2.0 only responds the client with an access token, which the resource server has to verify against authorization server. This is another round trip of HTTP request. However, it does enable authorization server to revoke of access token. For example, if I have logged on to stackoverflow.com using Facebook, but I am logging out of Facebook. The access token on stackoverflow.com will no longer be accepted by Facebook, so that I am logged out of stackoverflow as well. 



#SAML Mobile SSO Flow:

Regarding Mobile SSO support, SAML is more chatty than OAuth2

_credit to [here](http://stackoverflow.com/questions/23623200/authenticating-mobile-users-against-saml-idp)_

1. your mobile application opens WebView which accesses public URL of your SP
2. your SP starts authentication with IDP by sending redirect to the SAML IDP with AuthnRequest
3. user authenticates inside IDP's UI (which should be able to render properly for mobile clients as it's opened using a mobile client)
4. IDP redirects back to your SP with Response your SP application processes Response and generates a token usable with your Rest APIs 
5. SP communicates the token back to the mobile application (e.g. using WebViewClient + onPageFinished + cookies, or call to object provided with addJavascriptInterface, or whatever you already use)

#OAuth Flow: 

_credit to [here](http://www.slideshare.net/briandavidcampbell/is-that-a-token-in-your-phone-in-your-pocket-or-are-you-just-glad-to-see-me-oauth-20-and-mobile-devices)_

{% include image.html
            img="image/the-sso-world/oauth2-mobile.png"
            title="OAuth2 Web Flow"
            caption="" 
%}

As you can see, mobile app has to first make a request to Service Provider in SAML, instead of asking for an access token directly from the authorization server in OAuth2. 


Conclusion
------

SAML has been around for quite a while and is main stream amongst web apps. Though Mobile SSO would be trickier. 

OAuth2 has been adopted by many tech giants, Google, Facebook etc. and has better support for mobile. But depending on the services you are trying to integrate with, it might not be supported. 

There is not neccessarily the correct solution, but at least you can make informed decision by exploring both. 


