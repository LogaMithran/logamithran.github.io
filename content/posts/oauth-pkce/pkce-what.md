+++
date = '2025-04-28T20:52:02+05:30'
title = 'Unlocking PKCE: How Modern Apps Stay Secure Without Secrets'
+++

# What is PKCE?

##### PKCE - Proof key for code exchange

OAuth defines several grant types:

1. Authorization Code
2. Authorization Code with PKCE (recommended)
3. Client Credentials
4. Implicit Flow
5. Password Grant
6. Device Grant

These grant types are used to authenticate users to your application via an identity provider. Among them, types **1** and **2** are most commonly used in modern web applications.

![pkce_diagram](/pkce_diagram.svg)

# Why Should You Care?

With the Authorization Code grant, your client remains secure if it is a **confidential** client. However, for **public** clients (such as single-page applications), the client secret cannot be securely stored. Exposing it can lead to significant security vulnerabilities.

If you're using a single-page application backed by the Authorization Code grant, consider using **PKCE** to enhance your authentication flow.

# How to Use It

1️⃣ The user initiates login. Since authentication is handled by an external identity provider, the app builds a redirect URL with a `code_challenge`. This URL includes the callback URL and required scopes.

```http
client_id=<client_id>&code_challenge=<code_challenge>&code_challenge_method=<plain | S256>&redirect_uri=https://<host>/auth/callback&response_type=code&scope=openid
```

2️⃣ The `code_challenge` is derived from a `code_verifier`, a high-entropy, cryptographically secure random string (typically 128 bits).

```ruby
random_bytes = SecureRandom.random_bytes(32)
code_verifier = Base64.strict_encode64(random_bytes).tr('+/', '-_').delete('=')
code_challenge = Base64.strict_encode64(code_verifier)
```

3️⃣ Redirect the user to the identity provider:

```http
https://<host>/protocol/openid-connect/auth?client_id=<client_id>&code_challenge=<code_challenge>&
    code_challenge_method=S256&prompt=consent&redirect_uri=https://<host>/admin/auth/callback&
    response_type=code&scope=openid
```

4️⃣ The user authenticates. The identity provider validates the credentials and redirects the user to the specified callback URL with an authorization code.

5️⃣ & 6️⃣ The application exchanges the authorization code for an `access_token` and `refresh_token`. The original `code_verifier` **must** be included in the request payload.

If the `code_verifier` is missing, you’ll encounter this error:

```shell
invalid_grant :: PKCE code verifier not specified
```

7️⃣ Upon successful verification of the `code_verifier`, the identity provider returns the `access_token`, `id_token`, and `refresh_token`.

Your application can now securely access protected resources on behalf of the user.