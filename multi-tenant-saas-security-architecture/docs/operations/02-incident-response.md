
---

### `docs/operations/02-incident-response.md`

```md
# Incident Response

This document defines the incident-response lifecycle for security incidents affecting the multi-tenant SaaS platform.

The primary objective is to contain security incidents quickly while preserving sufficient evidence for investigation and recovery.

---

## 1. Incident Response Lifecycle

The platform follows the lifecycle:

```text
Detection
   ↓
Containment
   ↓
Investigation
   ↓
Eradication
   ↓
Recovery
   ↓
Post-Mortem```


2. Severity Model
Security incidents should be classified according to business impact and technical severity.
SEV-1 — Critical
Immediate response required.
Examples:
- Confirmed cross-tenant data leak
- Platform control-plane compromise
- Platform Admin compromise
- Mass data exfiltration
- Large-scale credential compromise
- Multi-tenant impact
- Destructive production compromise
SEV-2 — High
Significant compromise with limited scope or strong potential for escalation.
Examples:
- Single-tenant administrative compromise
- Confirmed privileged-account misuse
- Sensitive credential exposure
- Serious production vulnerability under active exploitation
SEV-3 — Medium
Security issue with limited impact and no evidence of broad compromise.
SEV-4 — Low
Low-risk security event requiring normal investigation or remediation.
3. SEV-1 Triggers
The following must automatically be considered potential SEV-1 events:
- Cross-tenant data leakage
- Control-plane compromise
- Mass data exfiltration
- Compromise of global administrative capabilities
- Large-scale authentication bypass
- Broad secrets/KMS compromise
- Major tenant-isolation failure
4. First Response: Containment
Containment must begin before full root-cause analysis when active compromise is confirmed.
Potential containment actions include:
- Revoke affected sessions
- Add compromised session/token identifiers to revocation state
- Revoke API credentials
- Disable affected accounts
- Isolate compromised containers/workloads
- Restrict compromised tenant access
- Block malicious network sources
- Disable compromised integration
- Remove temporary privileged access
Example:
Confirmed Session Compromise
        ↓
Revoke Session in Server-Side State
        ↓
Invalidate Refresh Credential
        ↓
Prevent New Access
        ↓
Begin Detailed Investigation
5. Evidence Preservation
Containment actions must preserve forensic evidence where practical.
Evidence may include:
- WORM audit logs
- SIEM events
- Authentication records
- API request metadata
- Infrastructure logs
- Container/runtime evidence
- Network telemetry
- Database audit information
- Secret-access history
Evidence must be protected from unauthorized modification.
6. Investigation
The investigation must determine:
- Initial attack vector
- Compromised identity
- Affected tenant(s)
- Affected resources
- Privileges obtained
- Data accessed or modified
- Credentials exposed
- Persistence mechanisms
- Lateral movement
- Timeline of attacker activity
Multi-tenant investigations must specifically determine whether the incident crossed tenant boundaries.
7. Eradication
After understanding the compromise, the response team should remove:
- Malicious code
- Persistence mechanisms
- Compromised credentials
- Unauthorized accounts
- Malicious integrations
- Vulnerable configuration
- Exploited infrastructure components
Affected vulnerabilities must be remediated before unrestricted service restoration where possible.
8. Recovery
Recovery may involve:
- Restoring affected services
- Deploying patched workloads
- Rotating credentials
- Rebuilding compromised infrastructure
- Restoring validated backups
- Re-enabling users
- Re-enabling integrations
- Increasing monitoring
Recovery must confirm that the attacker no longer retains access.
9. Credential Response
If credential compromise is suspected:
1. Revoke affected credential.
2. Revoke associated sessions.
3. Rotate related credentials.
4. Review historical use.
5. Determine affected tenant/resources.
6. Monitor replacement credentials.
7. Preserve evidence.
10. Tenant Isolation During Incident Response
Containment should minimize unnecessary impact to unaffected tenants.
Where technically possible, isolate:
- Affected user
- Affected session
- Affected tenant
- Affected service
before taking platform-wide actions.
Platform-wide containment is appropriate when the scope cannot be reliably bounded.
11. Emergency Authority
Emergency actions must be restricted to designated incident-response and security/operations personnel operating under documented procedures.
High-impact emergency actions should be attributable and logged.
Examples include:
- Service isolation
- Global token revocation
- Integration shutdown
- Administrative-account disablement
- Network containment
12. Post-Mortem
Every significant incident should produce a documented post-mortem covering:
- Incident timeline
- Root cause
- Attack path
- Affected assets
- Failed controls
- Successful controls
- Detection quality
- Containment effectiveness
- Corrective actions
- Architecture changes
- Assigned owners and deadlines
Post-mortems should feed improvements back into the threat model and control architecture.
