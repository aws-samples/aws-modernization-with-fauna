---
title: "Deploy Lab 1" 
chapter: true
weight: 1
---

# Deploy the lab

## In an AWS Event

Deploy Lab1 by running the command below:

```bash
cd Lab1/scripts
./deployment.sh -s -c
```

> -s and -c parameters here denotes that we are deploying both server and client side code.

We built the entire application described in the architecture diagram using the AWS Serverless Application Model (SAM) 
framework. The **deployment.sh** script orchestrates the entire deployment process (*using environment variables
you supplied in the __.env__ file*) including invoking the `sam deploy` command, which ultimately provisions
all the components encoded in the **/Lab1/server/template.yaml** CloudFormation file into the AWS account belonging to
the **AWS_PROFILE** set in the **.env** file (For the Cloud9 IDE instance, this should just be set to **default**). 

This process takes a few minutes to complete. The final lines of the output should look something like the following:

```shell
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
```

Once complete, head over to the next section and explore the serverless components that were just provisioned for you.