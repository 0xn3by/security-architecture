# Trust Boundaries

This document defines the primary trust boundaries within the multi-tenant SaaS platform and explains why data crossing each boundary must be treated according to different security assumptions.

## Trust Zones

The architecture contains the following trust zones:

* **Untrusted Internet / User Zone**
* **Application Zone**
* **Data Zone**
* **External Trusted Identity Provider**
* **External Security Monitoring / SIEM**

## 1. Internet / User Boundary

Traffic originating from users, browsers, and external clients must be treated as untrusted.

The platform cannot assume that a request is safe simply because it originates from a valid browser or an authenticated user.

This boundary represents the transition between the untrusted external environment and the SaaS application.

## 2. Application Zone Boundary

The Application Zone contains the internal business services responsible for processing tenant requests and enforcing application logic.

Primary services include:

* Identity & Tenant Service
* Project Service
* File Service
* Audit Service

Services should follow least-privilege access principles and must not receive unrestricted access to other services or data stores.

## 3. Application-to-Data Boundary

Application services should only access the data stores and data they explicitly require.

This limits the blast radius of a compromised service and prevents a single service from obtaining unrestricted access to tenant data.

Expected access patterns include:

* **Identity & Tenant Service** → identity, organization, membership, and role data
* **Project Service** → project and task data
* **File Service** → file metadata and object storage
* **Audit Service** → audit and security event data

Internal service status does not automatically grant access to all tenant data.

## 4. Identity Provider Trust Boundary

The Identity Provider (IdP) is an external dependency and therefore exists outside the SaaS application trust boundary.

The platform trusts the IdP only for the identity and authentication assertions required by the SaaS application.

Relevant authentication data may include:

* ID tokens
* Access tokens
* Identity claims
* User attributes

The IdP must not be treated as part of the internal application environment simply because the platform relies on it for authentication.

## 5. SIEM / Security Monitoring Boundary

The SIEM is an external downstream security system that receives security-relevant telemetry from the SaaS platform.

Examples include:

* Authentication events
* Role and permission changes
* User and resource activity
* Security alerts
* System monitoring events

The SIEM is outside the SaaS application boundary and should be treated as a separate security consumer.

## 6. Tenant Isolation

Every request must establish or derive the tenant context before business logic is executed.

Tenant context should be derived from trusted authentication or session information rather than directly trusting tenant identifiers supplied by the client.

Authorization must validate both:

* Tenant ownership of the requested resource
* User permission to perform the requested action

Internal services must not bypass tenant authorization simply because they operate within the Application Zone.

Cross-tenant access requires an explicit authorized relationship.

## 7. Privileged Access Boundary

Support and platform-administration activities use a separate privileged-access path.

Privileged access must remain logically distinct from normal customer access.

Administrative access must not automatically bypass tenant isolation or application authorization requirements.

## 8. File Processing Boundary

Uploaded files are treated as untrusted content.

The file-processing flow follows this model:

**User Upload → File Validation → Malware Scanning → Security Decision → Approved Storage or Quarantine**

Security conditions include:

* File type and actual content must be validated.
* Malicious or suspicious files must not be exposed to normal users.
* Suspicious files are placed into a quarantine state.
* Only files receiving an approved result become accessible.
* Quarantine storage must not be directly accessible by normal users.

## 9. File Storage Separation

File content and file metadata are stored separately.

* **Object Storage** stores the actual file content.
* **PostgreSQL** stores file metadata and object references.

File metadata may include:

* Tenant identifier
* File owner
* Object reference
* File name
* File type
* File size
* Processing status

Every file-download request must pass authorization checks before access to the stored object is granted.

## Trust Boundary Summary

| Boundary                             | Trust Model                  | Primary Security Purpose                                             |
| ------------------------------------ | ---------------------------- | -------------------------------------------------------------------- |
| Internet / User → SaaS               | Untrusted                    | Prevent untrusted requests from directly reaching internal resources |
| Application Zone → Data Zone         | Restricted / least privilege | Limit data access and reduce blast radius                            |
| SaaS → Identity Provider             | Limited external trust       | Consume trusted identity assertions without extending internal trust |
| SaaS → SIEM                          | External downstream consumer | Export security telemetry for monitoring and detection               |
| Normal User → Privileged Access Path | Separate privileged boundary | Isolate administrative and support operations                        |
| File Upload → Processing / Storage   | Untrusted content boundary   | Prevent malicious content from reaching trusted storage and users    |

## Security Principle

> Trust must not be inherited merely because a component is internal. Every service, data flow, tenant relationship, and privileged operation must be explicitly authenticated, authorized, and limited to the minimum access required.
