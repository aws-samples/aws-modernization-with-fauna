+++
title = "Fauna Account"
chapter = false
weight = 2
+++

## Signup for an account
* If you don't yet have a Fauna account, register for one [here](https://dashboard.fauna.com/register?ref=aws-serverless-saas-workshop)

## Create a database

* Login to the Fauna [dashboard](https://dashboard.fauna.com/?ref=aws-serverless-saas-workshop)
* Create a new database
  * Provide a name, e.g. **serverless_workshop**
  * Choose a [Region Group](https://docs.fauna.com/fauna/current/learn/understanding/region_groups),
    e.g. **United States (US)**
  * Click **Create**  
  ![Create database](/images/getting_started/CreateADatabase.png?width=470)

## Generate an API Key
  * In the new database you just created, run the following command in the web shell:
    ```
    Key.create({
      role: 'admin',
      data: {
        name: 'workshop-admin'
      }
    })
    ```
  * You will see an output similar to the following (you will see your own values for *id* and *secret*):
    ```
    {
      id: "362569567723061316",
      coll: Key,
      ts: Time("2023-04-20T23:08:54.680Z"),
      secret: "fnAFCBsZ0oAARPEoNMi6tANpiBMReshQtEp3GE0W",
      role: "admin",
      data: {
        name: "workshop-admin",
      },
    }    
    ```
  * Copy the value of your key's **secret** to a temporary location. You may not see it again if you clear the shell
    or start a new session.
