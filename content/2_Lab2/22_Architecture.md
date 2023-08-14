---
title: "Reviewing the architecture" 
chapter: true
weight: 2
---

# Reviewing the architecture

The diagram below depicts the architecture for Lab 2 that you deployed using the **./deployment.sh** script. 

![Lab2 architecture](/images/ServerlessSaas-Lab2.png?width=700)

## What was added?

### Self-registration user experience
We provide an example experience of an anyonymous user registering for their own tenant. The sample app now includes a login UI, which also displays sign-up option. Clicking on it allows you to provide data about your new tenant and submit that information to the system's registration service. This service will then create and configure all the resources needed to introduce a new tenant into the system.

![Self register](/images/Lab2/self_register.png?width=520)

### SaaS admin application
In addition to self-registration, the SaaS provider can also add and manage tenants. (In a multi-tenant SaaS environment, the SaaS provider would need a way to manage tenants: Create new ones, enable/disable them, add/update metadata, etc.). The sample admin app represents this experience and we'll be taking a closer look at it later.


### Shared services
The shared services refer to the common set of services that are core to any SaaS environment. These services provide all the mechanisms that are needed to have a universal view of how tenants are onboarded, managed, and operated in SaaS environment.

#### Tenant Registration
The Tenant Registration service allows new tenants to register themselves and onboard to your SaaS application. There are a few moving parts to this registration experience. This Tenant Registration service is responsible for orchestrating interactions with user management and tenant management that are part of the onboarding experience.

#### User Management
The User Management service allows us to add, update, disable and get users. The users are stored in Amazon Cognito, which is otherwise known as the Identity Provider in the architecture. 

#### Tenant Management
The Tenant Management service centralizes all of the configuration and operations that can be performed on a tenant. This includes get, create, update, activate, and disable tenant functionality. Tenant details are stored in our Fauna database.

## Tenant Onboarding Flow
The diagram below depicts the tenant onboarding process and how Registration service leverages other services to orchestrate the flow.

![Lab2 tenant onboarding flow](/images/Lab2/TenantOnboardingFlow.png)

The sign-up process for a tenant is a combination of a few steps. 

* Step 1: The tenant user accesses the self-registration form, where they provide their information along with the tier they intend to sign-up for. This kicks off the registration process by invoking the "registration" endpoint inside API Gateway.
* Step 2: The registration service calls the Tenant Management service to store tenant information captured by the app – Specifically a record (document) of the new tenant gets created in the **tenant** collection in Fauna.
* Step 3: The registration service invokes the User Management service to create a new tenant admin user. This creates a Cognito User Group inside the pooled user pool for that tenant. Then, a tenant admin user is created for the tenant inside the user pool. As part of creating the tenant admin user we must also associate this user with tenant specific attributes. This is achieved through Cognito's custom attributes. These custom attribues store certain tenant and user specific information. For this solution we've stored TenantId and User Role as custom attributes. The TenantId was retrieved from Step 2, after the tenant info is saved in the database.
* Step 4: We also store the user pool information for the tenant, based upon the data collected from the prior step. We call the Tenant Management service one more time, to update the newly created **tenant** record with this info.

## Parent-child database

When we create a tenant, we add a new document to the **tenant** collection. We also create a **tenantUser** document to store the association between the tenant record and the tenant admin user created in Cognito. And finally, a child database is created, and it is hydrated with the Order Microservice and Product Microservice's schema.

![Lab2 parent-child database](/images/Lab2/ParentChildDB.png?width=400)

{{% notice tip %}}
Our schema design to achieve multi-tenancy leverages a unique Fauna feature that allows the association of databases as children of another. The Fauna API key we manage belongs to the "parent" database, but it is allowed scoped access to each of it's "child" databases. <br/><br/>With this design, all our functions and queries are written as if there is only a single tenant. Up in the Lambda layer, a utility instantiates the Fauna client and scope it to the proper database (parent or specific child) depending on the context of the request. We dive into the details of this later in Lab 3. 
{{% /notice %}}

## Security

One thing you will notice when you inspect the code, is that the tenant registration service (**POST /registration**) doesn't require any authentication. This is by design since new tenants are unable to be authenticated. However, the endpoints that are used to create a tenant admin, create a tenant, and provision a user are protected. These endpoints can only be invoked from the Tenant Registration service.

To keep these internal endpoints private and protected, we have used the API Gateway Resource Policies feature. These policies ensure that the private REST entry points of our services are not publicly accessible.

Access to Fauna is authorized with an API Key, which is protected using KMS.

### Encrypted Parameter
![Lab2 parameter store KMS](/images/Lab2/ParameterStoreKMS.png?width=250)
Whenever a Lambda function needs to access Fauna, it retrieves the Fauna API Key from the Parameter Store. The API Key is stored encrypted, using an encryption key managed by KMS.


