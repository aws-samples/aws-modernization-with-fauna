+++
title = "AWS Account"
chapter = false
weight = 3
+++
{{% notice warning %}}
You are responsible for the cost of the AWS services used while running this workshop in your AWS account.
{{% /notice %}}

{{% notice warning %}}
Your account must have the ability to create new IAM roles and scope other IAM permissions.
{{% /notice %}}

{{% notice note %}}
If you already have an AWS account, and have IAM Administrator access, you can skip this section.
{{% /notice %}}

## Create an account 

1. If you don't already have an AWS account with Administrator access: [create
one now](https://portal.aws.amazon.com/billing/signup)

2. Once you have an AWS account, ensure you are following the remaining workshop steps
as an **IAM user** with administrator access to the AWS account:
[Create a new IAM user to use for the workshop](https://console.aws.amazon.com/iam/home?region=us-east-1#/users$new)

3. Provide a user name e.g. `serverless-saas-fauna-workshop`:
   * Check the **Provide user access to the AWS Management Console** checkbox
   * Select the **I want to create an IAM user** radio button
   * Select the options on how you want to manage the initial password.  
    ![Create User](/images/getting_started/CreateIAMAdminUser.png?width=700)
   * Click **Next**

4. Attach the AdministratorAccess IAM Policy:
   
    Select the **Attach Policies Directly** radio button, then check the **AministratorAccess** policy checkbox.
    ![Attach Policy](/images/getting_started/AttachPolicy.png?width=900)

5. Click through the next steps to review and complete the user creation process.

    A summary screen appears at the end:

    ![Console Signin Details](/images/getting_started/ConsoleSigninDetails.png?width=660)

    Navigate to the Console sign-in URL and login with the credentials you setup:

    ![IAM User Login](/images/getting_started/IAMUserLogin.png?width=270)

    From here on, we'll go through the workshop using this IAM user.