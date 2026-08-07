# Business Context

## Overview

The organization is building a multi-tenant B2B SaaS platform that enables companies to manage projects, tasks, files, and team collaboration through a shared cloud-hosted application.

Multiple customer organizations (tenants) use the same application, APIs, database cluster, and storage infrastructure. Each tenant must experience the platform as a logically isolated environment, with no ability to access or infer data belonging to other tenants.

## Primary Users

- Organization Owner
- Organization Administrator
- Member
- Guest
- Customer Support Engineer
- Platform Administrator

## Business Goals

- Provide secure collaboration for multiple organizations
- Support API access for integrations and automation
- Enable file sharing within a tenant
- Maintain auditability of administrative and user actions
- Scale to thousands of organizations and users

## Security-Critical Business Requirement

The platform must prevent cross-tenant access to projects, files, users, API keys, audit logs, and administrative data.

## Assumptions

- The platform is hosted in a public cloud environment.
- Authentication is provided through a centralized identity provider supporting OAuth 2.1 / OIDC.
- Object storage is private by default.
- Customer data is considered confidential.

## Out of Scope

- Billing and subscription management
- Real payment processing
- Mobile application implementation
- Physical infrastructure security
- Detailed cloud network configuration
