---
title: Admin actions
description: Some Description
---

In some scenarios you may wish to expose Administrative actions to your end user applications. For example, the ability to list all users in a Cognito User Pool may provide useful for the administrative panel of an app if the logged-in user is a member of a specific Group called "Admins". 

> This is an advanced feature that is not recommended without an understanding of the underlying architecture. The associated infrastructure which is created is a base designed for you to customize for your specific business needs. We recommend removing any functionality which your app does not require.

The Amplify CLI can setup a REST endpoint with secure access to a Lambda function running with limited permissions to the User Pool if you wish to have these capabilities in your application, and you can choose to expose the actions to all users with a valid account or restrict to a specific User Pool Group.

## Enable Admin Queries

```bash
 amplify add auth
 ```

 ```console
? Do you want to add an admin queries API? Yes
? Do you want to restrict access to a specific Group Yes
? Select the group to restrict access with: (Use arrow keys)
❯ Admins 
  Editors 
  Enter a custom group 
```

This will configure an API Gateway endpoint with a Cognito Authorizer that accepts an Access Token, which is used by a Lambda function to perform actions against the User Pool. The function is example code which you can use to remove, add, or alter functionality based on your business case by editing it in the `./amplify/backend/function/AdminQueriesXXX/src` directory and running an `amplify push` to deploy your changes. If you choose to restrict actions to a specific Group, custom middleware in the function will prevent any actions unless the user is a member of that Group.

## Admin Queries API

The default routes and their functions, HTTP methods, and expected parameters are below
- `addUserToGroup`: Adds a user to a specific Group. Expects `username` and `groupname` in the POST body.
- `removeUserFromGroup`: Removes a user from a specific Group. Expects `username` and `groupname` in the POST body.
- `confirmUserSignUp`: Confirms a users signup. Expects `username` in the POST body.
- `disableUser`: Disables a user. Expects `username` in the POST body.
- `enableUser`: Enables a user. Expects `username` in the POST body.
- `getUser`: Gets specific user details. Expects `username` as a GET query string.
- `listUsers`: Lists all users in the current cognito identity pool. You can provide an OPTIONAL `limit` as a GET query string, which returns a `NextToken` that can be provided as a `token` query string for pagination.
- `listGroupsForUser`: Lists groups to which current user belongs to. Expects `username` as a GET query string. You can provide an OPTIONAL `limit` as a GET query string, which returns a `NextToken` that can be provided as a `token` query string for pagination.
- `listUsersInGroup`: Lists users that belong to a specific group. Expects `groupname` as a GET query string. You can provide an OPTIONAL `limit` as a GET query string, which returns a `NextToken` that can be provided as a `token` query string for pagination.
- `signUserOut`: Signs a user out from User Pools, but only if the call is originating from that user. Expects `username` in the POST body.

## Example

To leverage this functionality in your app you would call the appropriate route in your [JavaScript](~/lib/restapi/authz.md#cognito-user-pools-authorization), [iOS, or Android](~/sdk/api/rest.md#cognito-user-pools-authorization) application after signing in. For example to add a user "richard" to the Editors Group and then list all members of the Editors Group with a pagination limit of 10 you could use the following React code below:

```js
import React from 'react'
import Amplify, { Auth, API } from 'aws-amplify';
import { withAuthenticator } from 'aws-amplify-react';
import awsconfig from './aws-exports';
Amplify.configure(awsconfig);

async function addToGroup() { 
  let apiName = 'AdminQueries';
  let path = '/addUserToGroup';
  let myInit = {
      body: {
        "username" : "richard",
        "groupname": "Editors"
      }, 
      headers: {
        'Content-Type' : 'application/json',
        Authorization: `${(await Auth.currentSession()).getAccessToken().getJwtToken()}`
      } 
  }
  return await API.post(apiName, path, myInit);
}


let nextToken;

async function listEditors(limit){
  let apiName = 'AdminQueries';
  let path = '/listUsersInGroup';
  let myInit = { 
      queryStringParameters: {
        "groupname": "Editors",
        "limit": limit,
        "token": nextToken
      },
      headers: {
        'Content-Type' : 'application/json',
        Authorization: `${(await Auth.currentSession()).getAccessToken().getJwtToken()}`
      }
  }
  const { NextToken, ...rest } =  await API.get(apiName, path, myInit);
  nextToken = NextToken;
  return rest;
}

function App() {
  return (
    <div className="App">
      <button onClick={addToGroup}>Add to Group</button>
      <button onClick={() => listEditors(10)}>List Editors</button>
    </div>
  );
}

export default withAuthenticator(App, true);
```
