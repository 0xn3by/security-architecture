# Authentication and Session Security

This document defines authentication and session-management controls for the multi-tenant SaaS platform.

The authentication model uses an external Identity Provider through OIDC and applies defense-in-depth controls around token validation, session lifecycle, revocation, and privileged authentication.

---

## 1. Authentication Architecture

The platform uses:

- External Identity Provider using OIDC
- Short-lived JWT access tokens
- Rotating opaque refresh tokens
- Server-side refresh-token state
- Token revocation support
- MFA for privileged identities
- Step-up authentication for sensitive operations

The Identity Provider is trusted only for explicitly validated identity assertions.

---

## 2. Access Token Model

JWT access tokens should be short-lived.

Before accepting a token, the application must validate:

- Cryptographic signature
- Approved signing algorithm
- `iss` - expected issuer
- `aud` - expected audience
- `exp` - expiration
- `nbf` - not-before, where present

Tokens that fail any required validation must be rejected.

The application must explicitly reject insecure or unsupported algorithms, including `alg: none`.

---

## 3. JWKS Signature Verification

Public signing keys should be obtained through the trusted Identity Provider's JWKS endpoint.

The system must:

- Verify signatures using trusted IdP keys
- Validate the expected signing algorithm
- Handle key rotation securely
- Reject unknown or invalid signing keys
- Avoid blindly trusting token-controlled key identifiers

Cryptographic validation must occur before identity claims are trusted.

---

## 4. Identity and Tenant Claims

Authentication proves identity but does not independently authorize access.

Claims obtained from a valid token may contribute to the security context, but authorization must additionally validate:

1. Authenticated identity
2. Tenant membership
3. Resource ownership
4. Requested operation
5. User permission

Client-supplied tenant identifiers must not override trusted server-side tenant context.

---

## 5. Refresh Token Design

Refresh tokens should be:

- Opaque
- High entropy
- Rotated after use
- Stored server-side in protected form
- Revocable
- Bound to the relevant user/session

Refresh-token rotation should invalidate the previous token after successful exchange.

Reuse of a rotated refresh token should be treated as a potential session-compromise signal.

---

## 6. Session Revocation

The system must support revocation for situations including:

- User logout
- Credential compromise
- Administrative session termination
- Role removal
- Suspicious activity
- Account disablement

Where immediate JWT revocation is required, the platform may maintain revoked token or session identifiers in a low-latency datastore such as Redis.

The revocation mechanism must not replace normal token expiry.

---

## 7. Session Lifecycle

Sessions should have defined:

- Access-token lifetime
- Refresh-token lifetime
- Idle timeout
- Maximum session lifetime
- Rotation behavior
- Revocation behavior

Long-lived unrestricted sessions should be avoided.

Sensitive actions may require recent authentication even when an existing session remains valid.

---

## 8. Step-Up Authentication

Step-up authentication should be required for high-risk operations.

Examples include:

- Changing high-privilege roles
- Organization ownership transfer
- Administrative impersonation
- Security-setting changes
- Credential management
- Destructive tenant operations

For privileged administrators, phishing-resistant MFA such as FIDO2 security keys should be preferred.

---

## 9. Privileged Authentication

Platform Admin and Support identities must use stronger authentication requirements than normal tenant users.

Controls should include:

- MFA
- JIT privileged access
- Step-up authentication
- Dedicated administrative identities
- Restricted administrative access path
- Strong session monitoring

Standing administrative privileges should be minimized.

---

## 10. Session Protection

Session and authentication artifacts must be protected against theft and replay.

Where browser cookies are used, appropriate controls should include:

- `Secure`
- `HttpOnly`
- Appropriate `SameSite`
- Restricted cookie scope

Sensitive tokens must not be exposed unnecessarily to client-side JavaScript.

Tokens must not be included in URLs or insecure logs.

---

## 11. Authentication Failure Handling

Authentication failures should:

- Return generic failure responses where appropriate
- Avoid exposing unnecessary account information
- Generate security telemetry
- Support rate limiting
- Detect repeated suspicious failures

Repeated authentication failures may trigger additional detection or temporary controls.

---

## 12. Logging Requirements

Security-relevant authentication events should include:

- Successful login
- Failed login
- MFA challenge
- MFA failure
- Refresh-token use
- Session revocation
- Logout
- Suspicious token reuse
- Privileged authentication

Logs must not contain raw passwords, access tokens, refresh tokens, or other reusable credentials.

---

## Security Principle

> Authentication establishes who the caller is. It must never be treated as proof that the caller is authorized to access a tenant, resource, or privileged operation.
