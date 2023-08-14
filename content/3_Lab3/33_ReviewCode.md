---
title: "Review the code" 
chapter: true
weight: 3
---

# Reviewing the code

Return to your IDE and navigate to the **Lab3/server/Resources** folder. Open up the *tenant_authorizer.py* file. The handler in the file is responsible for validating the JWT from Cognito. In addition, it will extract relevant claims from the JWT and inject these values into every Lambda request. Critically, on line 44, we access the tenant_id from the validated JWT. Then on line 62, we add the value into the context field of the authorizer's response. 

{{<highlight py "linenos=inline,hl_lines=12 30, linenostart=33">}}
    #authenticate against cognito user pool using the key
    response = validateJWT(jwt_bearer_token, appclient_id, keys)
    
    #get authenticated claims
    if (response == False):
        logger.error('Unauthorized')
        raise Exception('Unauthorized')
    else:
        logger.info(response)
        principal_id = response["sub"]
        user_name = response["cognito:username"]
        tenant_id = response["custom:tenantId"]
    
    tmp = event['methodArn'].split(':')
    api_gateway_arn_tmp = tmp[5].split('/')
    aws_account_id = tmp[4]    
    
    policy = AuthPolicy(principal_id, aws_account_id)
    policy.restApiId = api_gateway_arn_tmp[0]
    policy.region = tmp[3]
    policy.stage = api_gateway_arn_tmp[1]

    #roles are not fine-grained enough to allow selectively
    policy.allowAllMethods()        
    
    authResponse = policy.build()
 
    context = {
        'userName': user_name,
        'tenantId': tenant_id        
    }

    authResponse['context'] = context
    
    return authResponse

{{</highlight>}}

This context then gets injected into every Lambda invocation. Let's take a look again at a handler we previously saw. Navigate to the **Lab3/server/ProductService** folder. Open up the *product_service.py* file and take a look at how the **create_product** function has been modified from Lab1. 

In line 51 below, we can now grab the **tenantId** from the requestContext. And in line 58, we use this value to instantiate the Fauna client.

{{<highlight py "linenos=inline,hl_lines=2 9, linenostart=56">}}
def create_product(event, context):    
    tenantId = event['requestContext']['authorizer']['tenantId']
    tracer.put_annotation(key="TenantId", value=tenantId)

    logger.log_with_tenant_context(event, "Request received to create a product")
    payload = json.loads(event['body'])
    try:
        global clients
        db = FaunaClients(clients, tenantId)

{{</highlight>}}

Instantiating a Fauna client simply requires an API Key, which we already have. And to alter the "scope" of the key for child database access, we only need to append the name of the child database to the key.

A basic example of how to instantiate the Fauna client for the child database is shown below:

```py
from fauna.client import Client as FaunaClient

...

client = FaunaClient(
    secret="{}:tenant_{}:server".format(parent_api_key, tenant_id)
)
```

...where we append the child database name (recall from Lab 2, it is in the format **tenant_{tenant_id}**) and a database key role, i.e. **server** to the parent database's key, delimited by a colon character, "**:**". 

Essentially, the only real changes – to our Microservices code – between Lab 1 and this Lab is in grabbing the tenantId from the request context and instantiating a Fauna client with scoped access to a child database. With these 2 chances our Microservice has been modified for multi-tenancy!