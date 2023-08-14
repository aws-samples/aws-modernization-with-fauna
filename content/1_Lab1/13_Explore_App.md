---
title: "Explore the web app" 
chapter: true
weight: 3
---

# Exploring our Serverless Web Application

Back at the terminal/shell where you ran the **./deployment.sh** script, notice that the last line
outputs the **Application site URL**. 

{{<highlight shell "linenos=false,hl_lines=12">}}
vite v3.2.4 building for production...
✓ 35 modules transformed.
dist/index.html                  0.45 KiB
dist/assets/index.53369baf.css   16.29 KiB / gzip: 3.81 KiB
dist/assets/index.03a20a87.js    105.12 KiB / gzip: 38.20 KiB
aws s3 sync --delete --cache-control no-store dist s3://serverless-saas-fauna-workshop-lab1-appbucket...
upload: dist/index.html to s3://serverless-saas-fauna-workshop-lab1-appbucket...
upload: dist/vite.svg to s3://serverless-saas-fauna-workshop-lab1-appbucket...
upload: dist/assets/index.53369baf.css to s3://serverless-saas-fauna-workshop-lab1-appb...
upload: dist/assets/index.03a20a87.js to s3://serverless-saas-fauna-workshop-lab1-appbuck...
Completed configuring environment for App Client
Application site URL: https://dzbjyqppcfnn5.cloudfront.net
{{</highlight>}}


Copy the Application site URL and launch it in a browser. You will see the web application:

![Lab1 Sample App](/images/Lab1/sample_app.png?width=630)

## Products

Let's use this application to interact with our API. Click on the *Products* tab on the left-hand navigator.
Notice that the product(s) you previously added is displayed:

![Lab1 Sample App GET Products](/images/Lab1/sample_app_products.png?width=730)

Add some more products, e.g.
![Lab1 Add Product](/images/Lab1/add_product_ui.png?width=310)

Clicking **Save** essentially calls the **POST /product** API we reviewed in the previous section, ultimately 
sending a [product.create({object})](https://fqlx-beta--fauna-docs.netlify.app/fqlx/beta/reference/schema_entities/collection/instance-create)
query to Fauna and creating a document in the **product** collection.

## Orders

Using the left-hand navigator, click on the *Orders* tab, then use the **Create Order** button to create an order.
<u>Add at least 2 different products</u> to the order, and click **Save**.
![Lab1 Create Order](/images/Lab1/create_order_ui.png?width=630)

The *Orders* view lists all the recent orders, including the order you just created above.
An Order can have multiple Products associated with them. Notice that we display the associated Products,
as well as their information together with the Order. 
![Lab1 List Orders](/images/Lab1/list_orders.png?width=630)

In Fauna, the Order document is stored with only pointers to the Products.
Navigate to the Fauna dashboard and click on the Order document to examine it's structure:
![Lab1 order](/images/Lab1/lab1_order.png)

Notice that only a "pointer" to the Products are stored in the Order document while in the in the sample app's UI, 
we're able to see the Products' details. One way to implement this is to query the
Order and the Product(s) separately, then join the results in our code. But this is not how we did this.
Instead, we used the Fauna query language to perform cross document joins, eliminating the need for multiple
requests to the database, which would have caused increased latency. 

## Review the code
In the next section, let's examine the underlying code and how the above query is constructed.


