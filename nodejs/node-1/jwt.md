# JWT

* 1
* 1
* 1
* 1

A JSON Web Token (**JWT**) is a compact, URL-safe, digitally signed token used for authentication and information exchange.

It's properties are:

> <mark style="color:yellow;">Scalability</mark>: JWTs can be easily generated, distributed, and verified across multiple servers due to being stateless and self-contained.
>
> <mark style="color:yellow;">Flexibility</mark>: JWT tokens can be used across multiple domains, devices, protocols and services, including APIs and databases.
>
> <mark style="color:yellow;">Security</mark>: JWT ensures its authenticity through a **digital signature**, which prevents any editing during transmission. This signature is generated using a cryptographic **sign()** method that combines the user data stored in the token with a secret key, and is then appended to the token.
>
> <mark style="color:yellow;">Performance</mark>: As **self-contained** tokens, JWTs enable fast verification, outperforming traditional session-based authentication methods that require database queries.
>
> <mark style="color:yellow;">Safe on transmission</mark>: JWT can be protected from external scripts and session fixation attacks during transmission. It can be sent in the Authorization header, or via cookies that require additional security flags (e.g., httpOnly and secure) but offer the benefit of a built-in expiration date
>
> <mark style="color:yellow;">Easy revocation</mark>: JWTs can be revoked by deleting the token, eliminating the need to invalidate sessions.

1

1

1

1

1

1
