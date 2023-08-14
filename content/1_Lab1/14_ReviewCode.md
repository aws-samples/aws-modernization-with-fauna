---
title: "Review the code" 
chapter: true
weight: 4
---

# Reviewing the code

Go back to your IDE and expand the Lab1 folder. You will see two sub-folders, *client* and *server*. 
The *client* sub-folder contains the project for the front end. 
The *server* sub-folder contains the code used to deploy the AWS infrastructure. 
Inside *server*, you will notice we have *OrderService* and *ProductService* folders, 
which represent our product and order microservices. Also notice the *template.yaml* file; 
As mentioned before, it file encodes the infrastructure that we deployed using the Serverless Application Model (SAM).

## Continuing where we left off

Continuing from where we left off from in previous section, lets see how the query is written for the **GET /orders** API.
Under the *OrderService* folder, open the *order_service.py* file:

![Lab1 VS Code](/images/Lab1/Lab1 VSCode.png?width=240)

Examine the **get_orders** function on line 179:

{{<highlight py "linenos=inline,hl_lines=10-25,linenostart=179">}}
def get_orders(event, context):
    logger.info("Request received to get all orders")
    try:
        global db
        if db is None:
            db = Fauna.from_config(load_config())

        response = db.query(
            fql("""
            order.all().map(o=>{
              {
                id: o.id,
                orderName: o.orderName,
                creationDate: o.creationDate,
                status: o.status,
                orderProducts: o.orderProducts.map(x=>{
                  price: x.price,
                  quantity: x.quantity,
                  productId: x.product.id,
                  productName: x.product.name,
                  productSku: x.product.sku,
                  productDescription: x.product.description
                })
              }
            })
            """)
        )        
        logger.info("Request completed to get all orders")
        orders = response.data.data
        return utils.generate_response(orders)
    except Exception as e:
        return utils.generate_error_response(e)
{{</highlight>}}

Copy the query highlighted and run it in the Fauna web shell. Here's a sample screenshot:

![GET orders in shell](/images/Lab1/shell_get_orders.png?width=770)

Notice that even though the **order** and **product** details are in separate documents, we're able to combine them
in a single query. 
Back at the Lambda function, the exact output you see above is returned from the Fauna client, encoded to a JSON object and returned to the caller.

{{% notice tip %}}
Leveraging Fauna's ability to make cross document joins allows us to save on bandwidth and latency. 
It also makes our code cleaner and easier to understand.
{{% / notice %}}

## Lambda Layers
Inside the *server* sub-folder you will also notice we have a folder named *layers*. This folder contains a *logger.py* and a *utils.py* file, which were deployed as [Lambda Layers](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html). 
All of the other lambda functions share Lambda Layers in a centralized way. For example,
at the top of the *order_service.py* file, on line 6, we import from *logger.py*, and on line 8, we import from *utils.py*:

{{<highlight py "linenos=inline,linenostart=4">}}
import json
import utils
import logger

from utils import Fauna, load_config
from fauna import fql
{{</highlight>}}

Examine the code for the **Fauna** class and the **load_config** function in *utils.py*:

The **Fauna** class is just a subclass of 
**FaunaClient**, imported from the Fauna Python driver. We instantiate it and use it to send queries to the database. 
During instantiation, we provide it a *config* that contains a 
[Fauna API Key](https://docs.fauna.com/fauna/current/security/keys) for accessing our database.

```py
class Fauna(FaunaClient):
    @classmethod
    def from_config(cls, config):
        return cls(
            secret=config['FAUNA']['secret']
        )
```

The *config* is stored in
[AWS Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html).
And read back using the **load_config** helper function.

{{<highlight py "linenos=false,hl_lines=5">}}
def load_config():
    configuration = configparser.ConfigParser()
    config_dict = {}
    try: 
        param_details = boto_client.get_parameters_by_path(
            Path=FAUNA_CONFIG_PATH,
            Recursive=False,
            WithDecryption=True
        )
        if 'Parameters' in param_details and len(param_details.get('Parameters')) > 0:
            for param in param_details.get('Parameters'):
                config_dict.update(json.loads(param.get('Value')))
    except:
        logger.error("Encountered an error loading config from SSM.")
        traceback.print_exc()
    finally:
        configuration['FAUNA'] = config_dict
        return configuration
{{</highlight>}}

Right now, values are stored as plain text in the parameter store. In Lab2, we'll show you how to best secure them using
encryption and AWS KMS.

## Computing in context of data
We've previously seen the Fauna capabilities of making cross document joins, which improves the latency of our API. 
The benefits of the rich querying capability of the Fauna query language doesn't stop there. 
The query below is for the **POST /order** (aka "create order") API, and demonstrates another powerful feature
of the query language: <u>the abiliity to express business logic inside the query</u>.

Let's say the body of the request to the API is formatted as such:
```
{
    "orderName": "Example",
    "cart":
    [
        {
            "price": "6.27",
            "productId": "123456789101112131415",
            "quantity": 1
        },
    ]
}
```
where cart is a list of *productId*, *price* and *quantity* requested. Given that, let's now look at how Fauna
handles this payload:

{{<highlight py "linenos=inline,hl_lines=4 5 8 9, linenostart=59">}}
response = db.query(
    fql("""
        ${cart}.forEach(x=>{              
          let p = product.byId(x.productId)
          let updatedQty = p.quantity - x.quantity

          if (updatedQty < 0) {                  
            abort("Insufficient stock for product " + p.name + 
                  ": Requested quantity=" + x.quantity)
          } else {
            p.update({
              quantity: updatedQty,
              backordered: p.backorderedLimit > updatedQty
            })
          }
        })

        order.create({
          orderName: ${orderName},
          creationDate: Time.now(),
          status: 'processing',
          orderProducts: ${cart}.map(x=>{
            product: product.byId(x.productId),
            quantity: x.quantity,
            price: x.price
          })
        }) {
          id,
          orderName,
          creationDate,
          status,
          orderProducts
        }
        """,
        cart=payload['orderProducts'],
        orderName=payload['orderName']        
    )
)
{{</highlight>}}

* On line 61, we loop through the list of products.
* On line 62, given a *productId*, we retrieve the product information.
* On line 63, the payload tells us how much of the product is requested and we compare it to the product's quantity on hand.
* On line 66, if the quantity requested is greater than the quantity in stock, we abort the transaction.
* Otherwise, we update the product's quantity after subtracting the quantity requested, and finally we create the Order (line 76).

Being able to handle the business logic within the query provides 2 major benefits:

1. Improved latency, as we don't have to issue multiple requests to the database.
2. Transactionality - The entire query and every mutation either succeeds, aborts or fails. If we implemented the above with multiple requests, any failed requests will result in our data being partially updated.

## Onto the next lab
Now that you understand the baseline architecture, let's continue by adding necessary elements needed inside our SaaS application.