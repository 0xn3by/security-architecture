
---

### `05-secrets-and-key-management.md`

```md
# Secrets and Key Management

This document defines controls for protecting credentials, API keys, integration secrets, encryption keys, and other sensitive authentication material used by the multi-tenant SaaS platform.

---

## 1. Secret Classification

Secrets include:

- API keys
- Integration tokens
- OAuth client secrets
- Database credentials
- Service credentials
- Encryption keys
- Signing keys
- Administrative credentials
- External service tokens

Secrets must not be treated as ordinary configuration data.

---

## 2. Hashing vs Encryption

The protection mechanism depends on whether the original secret must ever be recovered.

### Verification-Only Secrets

Secrets used only for comparison or authentication should be stored as cryptographic hashes.

Examples:

- User-generated API keys
- Certain recovery tokens

Suitable password/key hashing mechanisms may include:

- Argon2
- bcrypt

The plaintext value should normally be displayed only once when the credential is initially created.

### Recoverable Secrets

Some credentials must later be presented to external services.

Examples:

- Third-party integration access tokens
- OAuth client secrets
- External API credentials

These cannot be protected using irreversible hashing alone.

They must be encrypted using controlled key-management infrastructure.

---

## 3. Secret Manager

Production secrets must be stored in a dedicated secrets-management system.

Examples of the required security model include:

- Centralized secret storage
- Strong access control
- Auditability
- Rotation support
- Controlled application retrieval

Production secrets must not be committed to source code.

---

## 4. Key Management Service

Encryption keys should be protected using a KMS or equivalent managed key infrastructure.

Application services should not store root encryption keys directly.

The KMS should control cryptographic operations and key lifecycle.

---

## 5. Envelope Encryption

Sensitive recoverable secrets may use envelope encryption.

Conceptually:

```text
Secret
→ encrypted using Data Encryption Key
→ Data Encryption Key protected by KMS-managed key



6. Source Code Protection
Secrets must not appear in:
- Git repositories
- Dockerfiles
- Application source code
- Build scripts
- Documentation examples containing real credentials
Secret scanning should be used in development and CI environments.
7. Environment Variables
Environment variables may be used as a delivery mechanism but should not be treated as the underlying secrets-management system.
Long-lived plaintext secret files such as committed .env files must not be used for production credentials.
8. API Key Generation
API keys must use cryptographically secure randomness.
Keys should have sufficient entropy to prevent guessing.
The system should associate each key with:
- Credential identifier
- Tenant
- Owner/integration
- Scope
- Creation timestamp
- Expiration
- Revocation status
9. API Key Storage
Verification-only API keys should be stored in hashed form.
Recommended lifecycle:
Generate Key
→ Display Plaintext Once
→ Store Hash
→ Authenticate Future Requests by Hash Verification
Plaintext API keys should not be retrievable after creation unless the architecture explicitly requires recoverability.
10. Credential Scoping
Credentials must follow least privilege.
Scopes may restrict:
- Read/write access
- Endpoint groups
- Resources
- Tenant
- Integration functionality
A credential should never receive platform-wide access unless explicitly required.
11. Credential Lifetime
Long-lived permanent credentials should be minimized.
The platform should prefer:
- Temporary tokens
- Expiring credentials
- Automated rotation
Credential lifetime should correspond to operational need and compromise risk.
12. Rotation
The system must support rotation of:
- API keys
- Integration credentials
- Database credentials
- Service credentials
- Encryption keys where appropriate
Rotation should be possible without extended service disruption.
13. Revocation
Credentials must be individually revocable.
Revocation should be available for:
- Compromise
- Employee/user departure
- Integration removal
- Role changes
- Suspicious behavior
- Administrative intervention
Revoked credentials must stop authorizing new requests.
14. Network and Client Restrictions
High-trust machine integrations may apply additional controls such as:
- IP allowlisting
- mTLS
- Workload identity
- Private network connectivity
These mechanisms provide defense in depth and must not replace credential authentication and authorization.
15. Service-to-Service Identity
Internal services should avoid shared static secrets where stronger workload-identity mechanisms are available.
Service identities should be:
- Unique
- Authenticated
- Least privileged
- Rotatable
- Auditable
Compromise of one service identity must not automatically compromise every internal service.
16. Key Separation
Cryptographic keys should be separated according to purpose.
A single key should not unnecessarily be reused for:
- Data encryption
- JWT signing
- Session protection
- External integrations
- Backup encryption
Purpose-specific keys reduce blast radius.
17. Secret Access Control
Access to production secrets must follow least privilege.
Only workloads and administrators with a legitimate requirement should be able to retrieve a secret.
Human access to production credentials should be minimized and audited.
18. Logging
The following must never be written to logs in reusable plaintext form:
- API keys
- Access tokens
- Refresh tokens
- Passwords
- Client secrets
- Encryption keys
- Database passwords
Logs may contain non-sensitive credential identifiers for correlation.
19. Monitoring
Security monitoring should detect:
- Unusual secret access
- Unexpected API-key usage
- Credential use from new networks
- Revoked credential attempts
- Excessive credential failures
- Administrative secret retrieval
- Secret rotation failures
20. Compromise Response
When credential compromise is suspected:
1. Revoke affected credential.
2. Rotate related secrets where necessary.
3. Investigate usage history.
4. Identify affected tenants/resources.
5. Review associated privileges.
6. Preserve audit evidence.
7. Issue replacement credential where appropriate.
