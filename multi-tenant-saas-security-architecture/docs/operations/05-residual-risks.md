
---

### `docs/operations/05-residual-risks.md`

```md
# Residual Risks

This document identifies security risks that remain after the planned preventive, detective, corrective, and recovery controls are implemented.

Residual risk exists because no technical architecture can eliminate every attack path or dependency failure.

---

## 1. Residual Risk Management

Residual risks may be handled using one or more strategies:

- **Mitigate** — introduce additional controls to reduce likelihood or impact
- **Transfer** — shift part of the financial/business exposure through mechanisms such as insurance or contractual agreements
- **Accept** — formally acknowledge that remaining risk is within organizational tolerance
- **Avoid** — remove or redesign the activity producing unacceptable risk

Risk should be mitigated to an acceptable threshold before acceptance whenever practical.

---

## 2. Zero-Day Vulnerability in Core Infrastructure

### Scenario

A previously unknown vulnerability affects critical infrastructure such as:

- Container runtime
- Kubernetes/container platform
- Operating system
- Network component
- Critical application dependency

Preventive vulnerability scanning may not detect the issue before exploitation.

### Existing Controls

- Container isolation
- Network segmentation
- Least privilege
- Egress restrictions
- Runtime monitoring
- Immutable logging
- Incident response
- Rapid patching

### Residual Risk

A sufficiently severe zero-day could allow code execution or infrastructure compromise before a patch becomes available.

### Treatment

**Mitigate / Accept**

Maintain defense in depth and rapid containment capabilities while accepting that zero-day exploitation cannot be completely eliminated.

---

## 3. Total Identity Provider Compromise

### Scenario

The trusted Identity Provider itself is compromised and begins issuing apparently valid authentication assertions to attackers.

Because tokens may be correctly signed, normal cryptographic JWT validation could succeed.

### Existing Controls

- Strict issuer/audience validation
- Short-lived access tokens
- Server-side tenant membership checks
- Authorization independent of authentication
- Privileged step-up authentication
- Behavioral monitoring
- Session revocation

### Residual Risk

A complete IdP compromise could undermine a major trust anchor of the platform.

### Treatment

**Mitigate / Transfer / Accept**

Reduce blast radius using authorization independence, behavioral detection, short-lived sessions, emergency identity shutdown procedures, and contractual/provider risk management.

---

## 4. Malicious Privileged Administrator

### Scenario

A legitimately authorized Platform Admin intentionally abuses privileged access.

### Existing Controls

- JIT access
- MFA
- Step-up authentication
- Network isolation
- Least privilege
- WORM audit logging
- Privileged activity monitoring

### Residual Risk

A privileged insider may still perform damaging actions within their legitimate technical authority.

### Treatment

**Mitigate**

Minimize standing privilege and enforce accountability through immutable logging and restricted privileged workflows.

---

## 5. Advanced Malware Scanner Evasion

### Scenario

A malicious file successfully bypasses file validation and malware scanning.

### Existing Controls

- Content validation
- Malware scanning
- Quarantine
- Processing isolation
- Restricted storage
- File authorization

### Residual Risk

Novel or specially crafted malware may evade signature or behavioral detection.

### Treatment

**Mitigate / Accept**

Maintain layered file controls and isolate processing so that scanner failure does not automatically become platform compromise.

---

## 6. Cloud Provider or Regional Outage

### Scenario

Cloud infrastructure or a critical region becomes unavailable.

### Existing Controls

- Backups
- Infrastructure-as-Code
- Defined RPO/RTO
- Restore testing
- Resilient architecture

### Residual Risk

Large-scale provider failures may exceed normal recovery assumptions.

### Treatment

**Mitigate / Transfer / Accept**

Additional multi-region or multi-provider architecture should be based on business availability requirements and cost.

---

## 7. Third-Party Dependency Compromise

### Scenario

A trusted external dependency such as:

- Identity Provider
- Monitoring provider
- Integration platform
- Software dependency

is compromised.

### Existing Controls

- Least privilege
- Egress allowlisting
- Scoped integration credentials
- Dependency scanning
- Credential rotation
- Monitoring

### Residual Risk

The platform depends on external systems outside its full security control.

### Treatment

**Mitigate / Transfer**

---

## 8. Residual Risk Acceptance

Formal acceptance of significant residual risk must include:

- Named risk owner
- Written risk description
- Business justification
- Existing controls
- Remaining impact
- Remaining likelihood
- Treatment decision
- Explicit expiration date
- Mandatory reassessment date

Risk acceptance must never be indefinite by default.

---

## 9. High-Risk Acceptance Authority

High or Critical residual risks require approval by appropriate senior technical/security leadership.

Examples include:

- CISO
- Head of Engineering
- Equivalent designated risk authority

The individual implementing a system should not unilaterally accept significant organizational risk without appropriate authority.

---

## 10. Risk Expiration

Accepted risks must expire.

At expiration, the organization must:

1. Reassess the threat.
2. Review whether circumstances changed.
3. Review available mitigations.
4. Decide whether to remediate, re-accept, transfer, or avoid the risk.

This prevents temporary exceptions from becoming permanent undocumented security debt.

---

## 11. Threat Model Review Cadence

The architecture and threat model must be reviewed:

**At least bi-annually**

and whenever a major architectural mutation occurs.

Examples include:

- New authentication system
- New tenant-isolation model
- New privileged-access capability
- Major infrastructure migration
- New sensitive data type
- New external trust dependency
- Major security incident

---

## Residual Risk Register

| Risk | Existing Protection | Treatment |
|---|---|---|
| Core infrastructure zero-day | Isolation, segmentation, monitoring, incident response | Mitigate / Accept |
| Complete IdP compromise | Authorization independence, short sessions, monitoring | Mitigate / Transfer / Accept |
| Malicious privileged administrator | JIT, MFA, immutable audit, least privilege | Mitigate |
| Malware scanner bypass | Quarantine, isolation, layered validation | Mitigate / Accept |
| Cloud/provider outage | Backups, IaC, recovery plan | Mitigate / Transfer / Accept |
| Third-party compromise | Scoped trust, credential controls, monitoring | Mitigate / Transfer |

---

## Security Principle

> Residual risk must be visible, owned, justified, time-bound, and periodically reassessed. A control architecture should reduce risk to an acceptable level without pretending that all security risk can be eliminated.```



