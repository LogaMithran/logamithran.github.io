+++
date = '2025-04-28T20:52:02+05:30'
title = 'Unlocking PKCE: How Modern Apps Stay Secure Without Secrets'
+++

# What is PKCE?

##### PKCE - Proof key for code exchange

There are different OAuth grant types like

1. Authorization code
2. Authorization code with PKCE (recommended)
3. Client credentials
4. Implicit flow
5. Password grant
6. Device grant

All these grant types are used for authenticating users to your application via an Identity provider. Out of these, 1 and 2 are used in most of the web applications.


![pkce_diagram](/pkce_diagram.svg)

# Why should you care about?
With the Authorization Code grant, your client remains secure if it's a confidential (private) client. However, for public clients, the client secret cannot be securely stored and exposing it can lead to security vulnerabilities

If you are having a single page application backed up by authorization code as your grant type then consider using PKCE in your authentication flow

# How to use it?

Step - 1
Building the redirect url
