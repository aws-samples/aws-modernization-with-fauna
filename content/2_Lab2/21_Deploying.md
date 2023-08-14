---
title: "Deploy Lab 2" 
chapter: true
weight: 1
---

# Deploy the lab

Let's deploy the lab before we dig into the details of the architecture. 
First we need to start with a fresh Fauna database.

## New Fauna database
1. From the Fauna dashboard, create a new database. 
   * Provide a name, e.g. **saas_workshop**
   * Choose a [Region Group](https://docs.fauna.com/fauna/current/learn/understanding/region_groups), e.g. **United States (US)**
   * Click **Create**  

    ![Create New Database](/images/Lab2/CreateDatabase.png?width=420)

2. Generate an API Key: In the new database you just created, run the following command in the web shell:

    ```js
    Key.create({
      role: 'admin',
      data: {
        name: 'workshop-admin'
      }
    })
    ```
    From the output of the above, copy the value of **secret** and temporarily save it somewhere for the next step.

## Deploy the lab

1. Back at your workstation/IDEs terminal, cd to **/Lab2/scripts**. Locate the **.env** file and edit in the values for **FAUNA_API_KEY**
    * Update the **FAUNA_API_KEY** variable to the **secret** value from the previous step

1. Run the following command to initialize the lab. Make sure to pass an email address that will be used to email you the credentials needed to login into the admin application.

    ```bash
    cd Lab2/scripts/
    ./deployment.sh -s -c --email <<provide your email to receive the Admin login credentials>>
    ```

The script will take a few minutes to complete. Once done, we need to perform a manual step to add a Fauna secret into Parameter Store.

## Parameter Store

1. Navigate to the Systems Manager Console, by searching for "systems manager" in the console search bar:
![Systems Manager](/images/Lab2/SystemsManager.png?width=550)

2. Notice an existing parameter */serverless-saas-fauna/faunadb/config/appConfig*. It was provisioned by the deployment script
![Parameters](/images/Lab2/Parameters.png)
  We'll come back and discuss what this in detail later on in the Lab. For now, just note that it is for storing
  plain text parameters, and was something we could automatically provision. 
  To store secrets, we need to manually create the parameter:

1. Click the **Create parameter** button to add a new parameter. Enter the following values into the form:
   * Name: **/serverless-saas-fauna/faunadb/config/appSecrets**
   * Type: **SecureString**
   * KMS key source: **My current account**
   * KMS Key ID: **alias/ServerlessSaaSFaunaWorkshopParameterStoreKey** (Note: this too was automatically provisioned)
   * Value: `{ "secret": "FAUNA_API_KEY" }` (Copy the value of FAUNA_API_KEY from the **.env** file and enter it here as the secret value)

    ![AppSecrets](/images/Lab2/AppSecrets.png)

2. Be sure to Save the parameter. Then let's move on to the next section. 