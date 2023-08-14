---
title: "Explore the Tenant App" 
chapter: true
weight: 4
---

# Tenant App

Let's now try to login as a tenant to the sample SaaS e-commerce application and enter some data.

Check your email for the temporary credentials you recieved for the "tenant1" tenant admin, which you onboarded during Lab 2.

![Temporary Password](/images/Lab3/EmailUsernamePassword.png)

Select the "Tenant app" radio button in the login modal and use the temporary credentials to login. You will be asked to change your password upon logging in the first time.

![Tenant App Login](/images/Lab3/TenantAppLogin.png?width=250)


Add a couple products, for example:

![Create Product](/images/Lab3/CreateProduct.png)

And create an order:

![Create Order](/images/Lab3/CreateOrder.png)


From the Fauna dashboard, navigate to the first child database. This should be the database for "tenant1":

![Tenant1 DB](/images/Lab3/FirstChildDB.png?width=700)

Examine the documents that were created in there under the product and order collections and verify that those were what you added using the UI.

![Fauna Daskboard](/images/Lab3/InspectFaunaUI.png?width=600)

Now navigate to the second child database. This should be the database for "tenant2":

![Tenant2 DB](/images/Lab3/SecondChildDB.png)

Verify that there are no documents in the product and order collections, as expected. 


## Wrapping it up

For good measure, logout, and login again. But now using the "tenant2" tenant admin. Add some products and orders and examine the database again to verify that the data was created in the proper child database.