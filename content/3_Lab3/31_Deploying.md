---
title: "Deploy Lab 3" 
chapter: true
weight: 1
---

# Deploy the lab

Let's initialize this lab before we dig into the details of the architecture. Run the following command to update the existing deployment (from Lab 2) with the latest code

```bash
cd Lab3/scripts
./deployment.sh -s -c
```

The script has lots to deploy, so it will take a few minutes. Now's a good time for a coffee break:

![Get some coffee](/images/Lab3/animated-coffee.gif)

Take note that once the deployment is complete, the App site URL is displayed on the last line, just like in the previous labs. Since this is an update of Lab 2, this should be the exact same URL, so if you haven't closed the browser tab to the Admin App from Lab 2, you can return to it and simply refresh the browser there.

{{<highlight shell "hl_lines=7">}}
...
vite v3.1.8 building for production...
...
upload: dist/assets/index.7fc7b1cf.js to s3://serverless-saas-fauna-userinterface-1...
Completed configuring environment for App Client
Successfully completed deploying Application UI
App site URL: https://dqsb7cy5638m1.cloudfront.net
{{</highlight>}}
