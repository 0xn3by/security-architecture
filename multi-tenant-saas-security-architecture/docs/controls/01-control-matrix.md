# Control Matrix

This document maps key threats in the multi-tenant SaaS platform to security requirements, preventive and detective controls, and verification methods.

| ID | Threat | Security Requirement | Primary Controls | Verification |
|---|---|---|---|---|
| CM-01 | Cross-tenant BOLA / IDOR | Enforce tenant and object-level authorization | Tenant-scoped object authorization, ownership checks, server-side permission validation | Negative authorization tests using Tenant A vs Tenant B objects |
| CM-02 | Vertical privilege escalation | Prevent unauthorized role or admin actions | Function-level authorization middleware, role allowlisting, server-side privilege checks | Attempt Member → Admin operations through direct API calls |
| CM-03 | Malicious file upload | Prevent unsafe files from becoming accessible | Content validation, malware scanning, quarantine pipeline, isolated object storage | Upload malicious/disguised files and verify quarantine |
| CM-04 | Audit-log tampering | Preserve security evidence | Centralized logging, append-only/WORM storage, restricted delete/update permissions | Attempt modification/deletion and verify rejection/audit trail |
| CM-05 | Forged or invalid JWT | Accept only valid trusted identities | Signature validation, algorithm allowlist, issuer/audience/expiry validation, short token lifetime, revocation where required | Test malformed, expired, wrong-audience and invalid-signature tokens |
| CM-06 | API credential theft | Reduce credential exposure and blast radius | Hashed storage where verification-only, strict scopes, short lifetimes, rotation, revocation, optional source/network restrictions | Secret leakage review, scope testing, rotation/revocation testing |
| CM-07 | API/file DoS | Prevent resource exhaustion | Rate limiting, payload limits, decompression limits, quotas, execution timeouts, concurrency limits | Load and abuse testing against defined limits |
| CM-08 | Platform Admin compromise | Limit privileged blast radius | MFA, JIT access, step-up authentication, network isolation, least-privilege admin roles, immutable logging | Privileged-access review and simulated compromised-admin scenarios |
| CM-09 | API over-fetching | Prevent unnecessary data disclosure | Response allowlisting, field-level authorization, minimal API schemas | Verify unauthorized/sensitive fields are never returned |
| CM-10 | Unauthorized file retrieval | Prevent cross-tenant file disclosure | File-level authorization, tenant ownership checks, short-lived scoped presigned URLs | Attempt access using another tenant's file identifier |
| CM-11 | Invitation/membership abuse | Prevent unauthorized tenant entry | Invite-token expiration, identity binding, tenant validation, role restrictions | Reuse, tamper, or replay invitation requests |
| CM-12 | Log flooding | Preserve monitoring availability | Event validation, rate controls, storage quotas, SIEM capacity monitoring | Generate high-volume events and verify controlled degradation |
| CM-13 | Service compromise | Limit lateral movement | Service authentication, service-level authorization, least-privilege datastore access | Verify each service cannot access unrelated stores/resources |
| CM-14 | Token/session theft | Reduce replay and account takeover | Secure cookies/session handling, short-lived tokens, revocation, re-authentication for sensitive actions | Replay and expired-session testing |
