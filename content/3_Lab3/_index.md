---
title: "Lab3: Adding multi-tenancy"
chapter: true
weight: 4
---

# Adding multi-tenancy to microservices

In this lab, we complete the multi-tenant system by merging the e-commerce app (introduced in Lab 1) with the tenant-admin app (introduced in Lab 2) into a single cohesive system.

![Lab 3 Microservices](/images/Lab3/Lab3Microservices.png)

We add multi-tenancy support to the application services by passing the tenant context while invoking the Lambda functions. The tenant context is all the information we need to implement the isolation mechanism required to ensure that only resources belonging to the users' tenant can be accessed.


{{% notice info %}}
The examples and sample code provided in this workshop are intended to be consumed as instructional content. These will help you understand how various AWS services can be architected to build a solution while demonstrating best practices along the way. These examples are not intended for use in production environments.
{{% /notice %}}

### Deploy the lab
Head over to the next section to deploy the sample app.
