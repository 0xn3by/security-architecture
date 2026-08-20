# Authorization and Tenant Isolation

This document defines the authorization model used to protect tenant-owned resources within the multi-tenant SaaS platform.

Tenant isolation is enforced using defense in depth rather than relying on a single authorization layer.

---

## 1. Authorization Model

The authorization decision follows a strict chain of trust:

Authenticated User
→ Tenant Membership
→ Resource Tenant Ownership
→ User Permission
→ Requested Operation

Every protected request must successfully satisfy the complete chain.

Authentication alone does not grant resource access.

---

## 2. Defense-in-Depth Authorization

Authorization must be enforced at multiple layers where appropriate.

### API / Entry Layer

The API entry point may perform:

- Authentication verification
- Basic route protection
- Request validation
- Rate limiting

It must not be treated as the only authorization layer.

### Service Layer

The responsible application service must enforce:

- Tenant membership
- Role permissions
- Resource ownership
- Function-level authorization
- Object-level authorization

### Data Layer

Tenant-owned database operations should additionally use tenant-scoped queries and database-level protections where appropriate.

PostgreSQL Row-Level Security may be used as an additional defense layer.

---

## 3. Tenant Context Establishment

Tenant context must come from trusted server-side information.

The system must not blindly trust a client-supplied:

- `tenant_id`
- `organization_id`
- `workspace_id`

The requested tenant must be validated against the authenticated user's active membership.

---

## 4. Object-Level Authorization

Every access to a tenant-owned object must verify authorization.

Examples include:

- Project
- Task
- Comment
- File
- User
- Membership
- Audit record
- Integration configuration

A valid object identifier does not grant access.

---

## 5. BOLA / IDOR Prevention

Requests such as:

```text
GET /projects/{project_id}
GET /tasks/{task_id}
GET /files/{file_id}


must validate that:
1. The user is authenticated.
2. The user belongs to the relevant tenant.
3. The object belongs to that tenant.
4. The user has permission to access the object.
5. The requested operation is allowed.
Changing a resource identifier must never expose another tenant's resource.
6. Horizontal Privilege Escalation
A user must not gain access to another user's or tenant's resources merely by modifying identifiers.
Authorization must prevent:
- Tenant A → Tenant B resource access
- User A → User B private resource access
- Unauthorized shared-resource access
Cross-tenant access must require an explicitly authorized relationship.
7. Vertical Privilege Escalation
Lower-privileged roles must not execute higher-privileged actions.
Examples:
- Member → Admin
- Member → Organization Owner
- Tenant Admin → Platform Admin
Sensitive functionality must use server-side function-level authorization.
Client-side UI restrictions are not security controls.
8. Role Management
Role assignment must validate both:
- Whether the caller may modify roles
- Whether the caller may assign the requested target role
Role parameters supplied by the client must be allowlisted.
A caller must not be able to assign privileges equal to or greater than their allowed administrative authority unless explicitly permitted.
9. Service-Level Isolation
Internal services must follow least privilege.
Examples:
- Identity & Tenant Service → identity, organization, membership, role data
- Project Service → projects and tasks
- File Service → file metadata and object storage
- Audit Service → audit data
Compromise of one service must not automatically provide unrestricted access to every datastore.
10. Database Isolation
Tenant-owned database queries must include appropriate tenant context.
Unsafe pattern:
SELECT * FROM projects WHERE project_id = ?
Preferred security model:
SELECT ...
FROM projects
WHERE tenant_id = trusted_tenant_context
AND project_id = requested_project
Additional permission checks must still apply.
Database filtering is not a replacement for application authorization.
11. Row-Level Security
Where PostgreSQL RLS is used, policies should restrict access according to the active trusted tenant context.
RLS acts as an additional security boundary against application-layer authorization failures.
RLS must not rely on attacker-controlled tenant values.
12. File Authorization
File access must validate:
- User identity
- Tenant
- File ownership
- Required permission
Object Storage must not be directly exposed as an authorization mechanism.
Presigned URLs must be generated only after successful authorization.
13. Support and Administrative Access
Support and Platform Admin access must use a separate privileged authorization path.
Controls should include:
- JIT privilege elevation
- MFA
- Step-up authentication
- Network restrictions
- Explicit tenant access
- Detailed audit logging
Administrative status must not silently bypass tenant-isolation controls.
14. Authorization Logging
Security-sensitive authorization events should include:
- Access denied
- Cross-tenant access attempts
- Role changes
- Membership changes
- Privileged operations
- Support access
- Repeated BOLA-like failures
These events should feed centralized monitoring.


