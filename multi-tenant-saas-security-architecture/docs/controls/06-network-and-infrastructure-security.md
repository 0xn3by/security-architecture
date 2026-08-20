# Network and Infrastructure Security

This document defines network and infrastructure security controls for the multi-tenant SaaS platform.

The architecture follows a defense-in-depth model in which public exposure is minimized, internal services operate within private network zones, administrative access is isolated, and both inbound and outbound connectivity are explicitly controlled.

---

## 1. Edge Architecture

Public application traffic follows the security path:

```text
Internet
   ↓
CDN / DDoS Protection
   ↓
Web Application Firewall
   ↓
Load Balancer / API Gateway
   ↓
Private Application Services```


Only components explicitly designed to receive public traffic should be internet-facing.
Internal application services and data stores must not be directly exposed to the public internet.
2. DDoS Protection
The platform should use upstream DDoS protection at the edge.
The DDoS layer should help absorb or mitigate:
- Volumetric attacks
- Protocol-level floods
- Large-scale request floods
- Network resource exhaustion
Application-level rate limiting must still exist because infrastructure DDoS protection does not replace API abuse controls.
3. Web Application Firewall
A WAF should be positioned at the public edge before application traffic reaches internal services.
In an AWS deployment, AWS WAF may be associated with an appropriate public entry component such as CloudFront or an Application Load Balancer.
The WAF may provide defense-in-depth protection against:
- Common web exploit patterns
- Known malicious payloads
- Automated scanning
- Bot traffic
- Suspicious request patterns
- Abusive source addresses
- Selected rate-based attacks
The WAF must not be considered a replacement for secure application logic.
For example, BOLA / IDOR and tenant-level authorization must still be enforced by the application.
4. Public and Private Network Separation
Application services should run within private network zones and should not accept direct inbound connections from the public internet.
Public traffic should reach internal services only through approved edge components.
The architecture should logically separate:
- Public / Edge Zone
- Private Application Zone
- Private Data Zone
- Privileged Administrative Access Zone
This provides a modern cloud equivalent of traditional DMZ-style segmentation.
5. Data Zone Isolation
Sensitive data infrastructure should remain within private network boundaries.
This includes:
- PostgreSQL
- Audit Store
- Internal object-storage access paths
- Internal supporting services
Database infrastructure must not expose a public administrative or database endpoint unless explicitly required and separately secured.
Application-to-database access should be restricted to authorized services.
6. Network Firewall and Security Group Controls
Network communication should follow deny-by-default principles.
Only explicitly required communication paths should be permitted.
Example:
Project Service
    ↓
PostgreSQL : TCP/5432
Allowing network connectivity does not grant application authorization.
Service and tenant-level authorization must still be enforced independently.
Network controls therefore provide an additional containment layer rather than replacing application security.
7. Service-to-Service Security
Internal service communication should use authenticated and encrypted channels.
mTLS may be used to provide:
- Workload authentication
- Encrypted east-west communication
- Protection against service impersonation
- Stronger internal trust establishment
Each workload should use a unique service identity.
mTLS proves which service is communicating but does not independently authorize what that service may do.
Service-level authorization must therefore restrict operations according to least privilege.
8. East-West Traffic Restriction
Internal services should not receive unrestricted network connectivity to every other internal component.
Required communication paths should be explicitly defined.
For example:
- Project Service → PostgreSQL
- File Service → Object Storage
- File Service → File Scanner
- Audit Service → Audit Store
Unnecessary service-to-service paths should be blocked.
This reduces lateral movement if a service is compromised.
9. Privileged Administrative Access
Platform Admin and Support access must use a separate privileged network path.
Administrative interfaces should not be directly exposed to the public internet.
Preferred access model:
Administrator
      ↓
ZTNA
      ↓
MFA
      ↓
Privileged Administrative Interface
      ↓
Private Application Environment
Privileged access should additionally use:
- JIT privilege elevation
- Step-up authentication
- Dedicated administrative identities
- Detailed audit logging
- Restricted network policies
10. Egress Filtering
Application services must not receive unrestricted outbound internet access.
Outbound connections should follow strict egress allowlisting.
Services should communicate only with approved external dependencies.
Examples may include:
- Identity Provider
- Required SaaS integrations
- Approved package or update endpoints
- Monitoring infrastructure
Egress restrictions reduce the ability of a compromised workload to:
- Exfiltrate data
- Communicate with command-and-control infrastructure
- Download malicious tooling
- Reach unauthorized external services
11. External Dependency Connectivity
Connections to trusted external systems must use encrypted transport.
Examples include:
- SaaS → Identity Provider
- SaaS → SIEM
- SaaS → External integrations
TLS certificate validation must remain enabled.
Where stronger machine-to-machine authentication is appropriate, mTLS or workload identity may be used.
12. Object Storage Network Controls
Object Storage should not be treated as a publicly browsable file server.
Access should be limited to authorized application paths.
Controls should include:
- Private storage configuration where appropriate
- Restricted service identities
- Object-level permissions
- Explicit application authorization before access
- Short-lived scoped presigned URLs when required
Knowledge of an object path must not provide unrestricted access.
13. Database Network Controls
PostgreSQL should accept connections only from approved workloads or network segments.
Controls should include:
- No unrestricted public access
- Security group or firewall restrictions
- TLS for database connections
- Strong workload/database authentication
- Least-privilege database accounts
- Connection limits
Network isolation complements tenant-scoped authorization and database controls such as Row-Level Security.
14. Network Monitoring
Security monitoring should capture relevant infrastructure events including:
- WAF blocks
- DDoS events
- Unexpected inbound traffic
- Rejected internal connections
- Suspicious outbound connections
- Administrative access
- ZTNA authentication events
- Network policy violations
Important events should be forwarded to centralized monitoring or SIEM infrastructure.
15. Network Security Model
The architecture follows the principle:
Untrusted Internet
       ↓
DDoS / CDN
       ↓
WAF
       ↓
Public Entry Layer
       ↓
Private Application Zone
       ↓
Private Data Zone
Privileged access follows a separate path:
Administrator
     ↓
ZTNA + MFA
     ↓
Privileged Access Layer
     ↓
Private Application Zone
Internal east-west traffic uses authenticated service identities and encrypted communication.
Outbound connectivity is restricted using egress allowlisting.
