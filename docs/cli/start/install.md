---
title: Installation
description: Installation
---  

## Pre-requisites

* [Install Node.js®](https://nodejs.org/en/download/) and [NPM](https://www.npmjs.com/get-npm) if they are not already on your machine.
* Verify that you are running at least Node.js version 10.x and npm version 6.x or greater by running `node -v` and npm -v in a terminal/console window


### Sign up for an AWS account

If you don't already have an AWS account, you'll need to create one in order to follow the steps outlined in this tutorial.

[Create AWS Account](https://portal.aws.amazon.com/billing/signup?redirect_url=https%3A%2F%2Faws.amazon.com%2Fregistration-confirmation#/start)

> There are no upfront charges or any term commitments to create an AWS account and signing up gives you immediate access to the AWS Free Tier.

## Install and configure the Amplify CLI

The Amplify Command Line Interface (CLI) is a unified toolchain to create AWS cloud services for your app. Let's go ahead and install the Amplify CLI.

### Option 1: Watch the video guide

Watch the video below to learn how to install and configure the Amplify CLI or skip to the next section to follow the step-by-step instructions.

<iframe
  allowfullscreen
  src="https://www.youtube.com/embed/fWbM5DLh25U"
></iframe>

### Option 2: Follow the instructions

```bash
npm install -g @aws-amplify/cli
```

> Because we're installing the Amplify CLI globally, you might need to run the command above with `sudo`.


Now it's time to setup the Amplify CLI. Configure Amplify by running the following command:

```bash
amplify configure
```

`amplify configure` will ask you to sign into the AWS Console.

Once you're signed in, Amplify CLI will ask you to create an IAM user.
> Amazon IAM (Identity and Access Management) enables you to manage users and user permissions in AWS. You can learn more about Amazon IAM [here](https://aws.amazon.com/iam/).

```console
Specify the AWS Region
? region:  # Your preferred region
Specify the username of the new IAM user:
? user name:  # User name for Amplify IAM user
Complete the user creation using the AWS console
```

Create a user with `AdministratorAccess` to your account to provision AWS resources for you like AppSync, Cognito etc.

![image](../../images/user-creation.gif)

Once the user is created, Amplify CLI will ask you to provide the `accessKeyId` and the `secretAccessKey` to connect Amplify CLI with your newly created IAM user.

```console
Enter the access key of the newly created user:
? accessKeyId:  # YOUR_ACCESS_KEY_ID
? secretAccessKey:  # YOUR_SECRET_ACCESS_KEY
This would update/create the AWS Profile in your local machine
? Profile Name:  # (default)

Successfully set up the new user.
```


### Work within your frontend project

After you install the CLI, navigate to a JavaScript, iOS, or Android project root, initialize AWS Amplify in the new directory by running `amplify init`. After a few configuration questions, you can use amplify help at any time to see the overall command structure. When you’re ready to add a feature, run `amplify add <category>`. 
