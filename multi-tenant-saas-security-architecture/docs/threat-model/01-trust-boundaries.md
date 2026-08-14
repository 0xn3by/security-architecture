# Trust Boundaries

Internet / User boundary
SaaS application boundary
IdP external trust boundary
SIEM external boundary
Application-services boundary
Data-store boundary


> so basically least-privilege data access across services not a unristricted access granted by anychance.
- Identity & Tenant Service accesses identity/tenant-related data
- Project Service accesses project/task data
- File Service accesses file metadata/object storage
- Audit Service writes/reads audit data
- Access should follow least privilege, not “every service talks to everything”

> file upload flow
User upload → treat as untrusted → validate actual file type/content → malware scan → quarantine if 
suspicious → only then make available.

> file uploads conditions
- Quarantine is not user-accessible
- Only clean files become available
- PostgreSQL stores file metadata/reference, not the binary itself
- Every download path must pass authorization before object access is granted

> 
- Every request has/derives tenant context 
- Authorization is enforced beyond just the gateway 
- Object-level checks validate both tenant ownership and user permission 
- Support/admin access uses a separate privileged path 

