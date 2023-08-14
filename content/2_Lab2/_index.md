---
title: "Lab2: SaaS shared services"
chapter: true
weight: 3
---

# Introducing SaaS shared services

Before we start working with tenants, lets introduce the services that allows us to onboard, authenticate, and manage the multi-tenant environment. The services, which are referred to as “shared services” will provide the core functionality that is *common* to all tenants. 

**Lab2 goals:**

1. Introduce **Shared services**, which enable the onboarding, authenticatication and management of a multi-tenant environment.

2. Multi-tenancy UX:
   * Allow end-users to self-register for a tenant
   * Provide a SaaS admin-console that a "super admin" uses to manage tenants. The app consumes
      the shared services to register, authenticate, and manage tenants.

3. Introduce the Fauna *parent-child database* construct, that allows for siloed segregation of tenant data.

### Deploy the lab
Head over to the next section to deploy the sample app.
