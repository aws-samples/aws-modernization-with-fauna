---
title: "Explore the Architecture" 
chapter: true
weight: 2
---

# Exploring the architecture

## In AWS

1. Go to the API Gateway Console, by searching for the API Gateway service inside of the AWS Management Console.
![API Gateway](/images/Lab1/APIGateway.png?width=700)

2. Notice an API has been deployed from the script you previously ran. Click on the name of the API and you'll see
   various *resources* (API routes) and *methods* (e.g. POST, GET, PUT, DELETE). Notice that each of them is
   associated with its *own Lambda function*. This approach allows you to scale each Lambda independently without
   affecting the experience of other Lambda functions.
![Lab1 API](/images/Lab1/Lab1API.png)

3. Let's take a closer look at the **POST /product** method by clicking into it, then click **Integration Request**:
![API Gateway methods](/images/Lab1/Post_Product.png)

4. You can see that the **Integration type** of this method is **Lambda**, and the specific **Lambda Function** is
   specified.
![POST Product](/images/Lab1/Post_Product_Lambda.png)

5. Note down the Lambda function's name, head over to the Lambda Console (search for "Lambda" inside the AWS Management
   Console search bar) and click into the Lambda function to view its definition.
![Lambda](/images/Lab1/Lambda.png?width=660)

6. Scroll down to *Runtime settings*, and notice that the *Handler* refers to the **create_product**
   function.
![Handler](/images/Lab1/Handler.png?width=600)

7. Inspect the source code. Scroll down to line 48 to the **create_product** definition and examine the code.
![Create Product Code](/images/Lab1/lambda_create_product.png?width=800)
   * On line 55 we instantiate a Fauna client. From this, we'll issue our "create product" queries to the database.
   * Lines 59-76 is the query we're issuing to the Fauna database. *Notice that we don't hardcode any values to the
     query but instead pass them in via string interpolation (lines 78-84) handled by the __fql()__ function*.
   * On line 88 we get the query's actual response, which is a JSON object.
   * On line 89, the Lambda returns the JSON object to the caller.

## In Fauna

1. Let's examine the Fauna dashboard. On the left-hand navigator, notice that 2 collections, **order** 
   and **product** were created for us by the deployment script. 
![Fauna dashboard](/images/Lab1/dashboard_left.png?width=700)

2. The syntax for creating a **product** document is [product.create({data})](https://fqlx-beta--fauna-docs.netlify.app/fqlx/beta/reference/schema_entities/collection/instance-create). 
   To see this in action, copy the query from the Lambda of step 7 above (lines 59-76), and insert actual values in place of the string interpolation expressions,
   e.g.
   ```js
    product.create({
      'sku': 'test-123',
      'name': 'Bananas',
      'description': '1 bunch. Priced per banana',
      'price': 0.39,
      'quantity': 100,
      'backorderedLimit': 10,
      'backordered': false
    }) {
      id,
      sku,
      name,
      description,
      price,
      quantity,
      backorderedLimit,
      backordered
    }   
   ```
   Paste it into the Fauna web shell, and click **Run**:
![Web shell](/images/Lab1/web_shell.png?width=660)
   * Notice that the output is JSON. This will be the same output that returns from the Fauna client in the Lambda,
     which we encode into a *JSON object* and return to the caler. The syntax between the curly brackets
     (*{ id, sku, name, ...}*) is called [**projection**](https://fqlx-beta--fauna-docs.netlify.app/fqlx/beta/reference/language/projection),
     and allows us to specify which fields we want to see returned in the response.
   * Also notice, the document appears in the left-hand resources navigator. Click on it to display and play around with the
     modal/editor


Thus far, we've given you a high-level idea of the moving parts of the serverless application, a taste of the Fauna query language,
and some familiarity with the AWS and Fauna web interface. To get a sense of completeness, head back to the API Gateway Console and 
examine the other methods, (e.g. **GET /products**, **DELETE /product**, **PUT /product**, **GET /orders**, etc), their corresponding
Lambdas, the Python implementations of the functions, and the Fauna queries contained within each of them.

## Explore the web app

Now let's head over to the next section to explore the web app included in this Lab.