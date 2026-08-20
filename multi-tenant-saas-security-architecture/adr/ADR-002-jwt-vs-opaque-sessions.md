
---

# 7. `adr/ADR-002-jwt-vs-opaque-sessions.md`

```md
# ADR-002: JWT Access Tokens vs Opaque Sessions

## Status

Accepted

## Context

The platform requires an authentication/session model suitable for distributed application services while retaining the ability to rapidly revoke compromised long-lived sessions.

Two competing requirements exist:

- Fast distributed access-token validation
- Strong server-side session lifecycle control

---

## Decision

Use a hybrid model:

**Short-lived JWT access token + rotating opaque refresh token**

---

## JWT Access Tokens

JWT access tokens are used for short-lived authorization context.

Application services can validate JWT signatures locally using trusted Identity Provider public keys.

Benefits include:

- Stateless routine validation
- Reduced central session-database dependency
- Efficient validation across distributed services
- Scalability

Validation must include:

- Signature
- Approved algorithm
- `iss`
- `aud`
- `exp`
- `nbf` where applicable

---

## Opaque Refresh Tokens

Refresh tokens are opaque random values.

They require server-side state lookup.

This allows the platform to:

- Revoke sessions
- Rotate refresh tokens
- Track token families
- Detect reuse
- Terminate compromised sessions

Opaque refresh-token state may be maintained in a low-latency datastore such as Redis or another suitable session store.

Stored token material should be protected appropriately.

---

## Refresh Token Rotation

Every successful refresh should issue a replacement refresh token.

The previous refresh token becomes invalid.

Reuse of an already rotated refresh token should be considered a compromise signal.

The platform may then invalidate the entire refresh-token family.

---

## Compromise Handling

When compromise is confirmed:

1. Revoke the active refresh-token family.
2. Remove or invalidate associated server-side session state.
3. Require full re-authentication.
4. Allow existing short-lived JWTs to expire naturally unless emergency revocation is required.
5. Where immediate access-token invalidation is necessary, maintain appropriate `jti` or session-level denylist state.

---

## Alternatives Considered

### Fully Stateful Opaque Sessions

Advantages:

- Immediate server-side revocation
- Simple centralized lifecycle control

Disadvantages:

- Every protected request may depend on centralized session lookup
- Greater runtime dependency and latency

### Long-Lived JWT Sessions

Advantages:

- Stateless
- Simple distributed validation

Disadvantages:

- Difficult immediate revocation
- Larger compromise window
- Long-lived bearer-token exposure

Rejected.

---

## Consequences

### Positive

- Fast access-token validation
- Strong refresh-session revocation
- Suitable for distributed services
- Reduced access-token lifetime
- Refresh-token reuse detection

### Negative

- More complex lifecycle design
- Stateful refresh infrastructure remains required
- JWT revocation is not instantaneous by default
- Key rotation and JWKS handling require careful implementation

---

## Security Principle

> JWTs are used for short-lived scalable validation, while opaque rotating refresh tokens preserve server-side control over long-lived session continuity.```
