# Disadvantages of JWT Authentication

- Limited Token Expiry Control: Once issued, JWTs remain valid until they expire. Revoking a JWT before expiration requires additional complexity, such as token blacklisting.
- No Token Invalidation: JWTs are valid until they expire, which can be problematic if a user’s access needs to be revoked immediately (e.g., due to a security breach).
- Limited Token Updates: JWTs are typically immutable once issued. If a user’s role or permissions change, they might need to log in again to get an updated token.
- Token Size: JWTs can become large if they carry extensive user data, leading to increased network traffic.
- Sharing Secrets in Microservices: If we ever plan to pivot to microservices, we'll need to share the secret key used to sign JWTs among different services, which introduces an element of complexity and a potential security concern if not managed correctly.

# Potential alternatives

## Session based auth 
 - Due to their stateless nature, JWTs are highly scalable. The server does not need to perform any database lookups or session management tasks, resulting in improved performance and reduced server load.
   On the other hand, session-based authentication relies on server-side sessions, which require storing session data on the server and maintaining state between requests.

## Short lived JWT + stateful refresh token + token blacklist 
 - Suffers from the similar disadvantages than existing approach but due to short lived nature, lots of disadvantages only exist for a short period. The Trade off being refresh tokens are still opaque strings with no data hence
needs to be stored and retrived from database when issuing new JWT tokens.
