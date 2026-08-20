# ADR-003: Centralized Authorization Policy with Local Enforcement

## Status

Accepted

## Context

The multi-tenant SaaS platform contains multiple application services requiring consistent authorization decisions.

Allowing every service to independently implement authorization policy creates risks including:

- Policy inconsistency
- Authorization drift
- Duplicated logic
- Inconsistent tenant isolation
- Difficult security review

At the same time, enforcing authorization solely at the API Gateway would lack the business and resource context required for fine-grained decisions.

---

## Decision

Use:

**Centralized authorization policy management with localized enforcement inside each responsible service.**

The architecture separates:

- Policy definition
- Policy evaluation
- Enforcement

---

## Enforcement Model

Conceptually:

```text
Request
   ↓
API Gateway
   ↓
Responsible Service
   ↓
Trusted Identity + Tenant + Resource Context
   ↓
Authorization Policy Evaluation
   ↓
Permit / Deny
   ↓
Service Enforcement```

The responsible service remains accountable for enforcing the final authorization decision.
API Gateway Role
The API Gateway may perform:
- Authentication validation
- Route-level restrictions
- Rate limiting
- Basic authorization gates
It must never be the sole authorization enforcement point.
The gateway lacks sufficient knowledge of:
- Resource ownership
- Tenant relationships
- Business rules
- Object-level permissions
- Fine-grained role conditions
Database Role
PostgreSQL RLS provides an additional isolation boundary.
RLS is not the primary business-authorization policy engine.
Its purpose is to reduce blast radius if higher-layer authorization or query scoping fails.
Fail-Closed Behavior
Authorization infrastructure must fail closed.
If required policy evaluation cannot be completed, protected operations must not default to allow.
Tradeoffs
Centralized authorization introduces architectural costs.
Availability Dependency
If the policy system becomes unavailable, protected operations may fail closed.
This creates an operational dependency requiring:
- High availability
- Resilience
- Monitoring
- Appropriate caching where safe
Latency
Remote policy evaluation may introduce additional request latency.
Possible mitigations include:
- Local policy agents
- Safe decision caching
- Efficient policy distribution
Complexity
Central policy management requires:
- Policy lifecycle management
- Versioning
- Testing
- Deployment controls
- Observability
Benefits
The selected design provides:
- Consistent authorization policy
- Easier security review
- Reduced policy duplication
- Central governance
- Service-specific enforcement
- Stronger tenant-isolation consistency
