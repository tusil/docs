
## Amazon Cognito Hosted UI

Amazon Cognito provides a customizable user experience via the Hosted UI. The Hosted UI is an OAuth 2.0 flow that allows you to launch a login screen without embedding an SDK for Cognito or a social provider into your application. The Hosted UI allows end-users to sign-in directly to your user pool through Facebook, Amazon, and Google, as well as through OpenID Connect (OIDC) and SAML identity providers. To learn more about Amazon Cognito Hosted UI, please visit [Amazon Cognito Developer Guide](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-configuring-app-integration.html).

### Automated Setup with CLI

You need to configure your identity providers(Google, Facebook or Login with Amazon) which you would like to use.

### Setting Up OAuth With Facebook

1. Create a [developer account with Facebook](https://developers.facebook.com/docs/facebook-login).
2. [Sign In](https://developers.facebook.com/) with your Facebook credentials.
3. From the *My Apps* menu, choose *Add New App*.
![Image](~/images/cognitoHostedUI/facebook1.png)
4. Give your Facebook app a name and choose *Create App ID*.
![Image](~/images/cognitoHostedUI/facebook2.png)
5. On the left navigation bar, choose *Settings* and then *Basic*.
![Image](~/images/cognitoHostedUI/facebook3.png)
6. Note the *App ID* and the *App Secret*. You will use them in the next section in the CLI flow.

### Setting up OAuth with Google

1. Go to the [Google developer console](https://console.developers.google.com).
2. On the left navigation bar, choose *Credentials*.
![Image](~/images/cognitoHostedUI/google5.png)
3. Create your OAuth2.0 credentials by choosing *OAuth client ID* from the *Create credentials* drop-down list.
![Image](~/images/cognitoHostedUI/google6.png).
4. Choose *Web application*.
5. Click *Create*.
6. Note the *OAuth client ID* and *client secret*. You will need them for the next section in the CLI flow.
7. Choose *OK*.

### Setting up OAuth with Login with Amazon
1. Create a [developer account with Amazon](https://developer.amazon.com/login-with-amazon).
2. [Sign in](https://developer.amazon.com/loginwithamazon/console/site/lwa/overview.html) with your Amazon credentials.
3. You need to create an Amazon security profile to receive the Amazon client ID and client secret. Choose Create a Security Profile.
![Image](~/images/cognitoHostedUI/amazon1.png)
4. Type in a Security Profile Name, a Security Profile Description, and a Consent Privacy Notice URL.
![Image](~/images/cognitoHostedUI/amazon2.png)
5. Choose Save.
6. Choose Client ID and Client Secret to show the client ID and secret. You will need them for the next section in the CLI flow.
![Image](~/images/cognitoHostedUI/amazon3.png)

Run the following command in your project’s root folder:

```bash
amplify add auth     ##"amplify update auth" if already configured
```

Select Default configuration with Social Provider (Federation):

```console
Do you want to use the default authentication and security configuration? 
  Default configuration 
❯ Default configuration with Social Provider (Federation) 
  Manual configuration 
  I want to learn more.
```

After going through the CLI flow, run the following command to deploy the configured resources to the cloud:

```bash
amplify push
```

After running the `amplify push` command, you will find a domain-name provisioned by the CLI for the hosted UI as an output in the terminal. You can find that information anytime later using the `amplify status` command.

**Note:** your user pool domain is something like: `domain_prefix-<env-name>.auth.<region>.amazoncognito.com`. 
If you've setup federation through third party providers, you would need to update the providers with the CLI provisioned domain-name.

### Setting up Hosted UI Domain With Facebook

1. [Sign In](https://developers.facebook.com/) with your Facebook credentials.
2. From the *My Apps* menu, choose *Your App*.
![Image](~/images/cognitoHostedUI/facebook1.png)
3. On the left navigation bar, choose *Settings* and then *Basic*.
![Image](~/images/cognitoHostedUI/facebook3.png)
4. Choose *+ Add Platform* from the bottom of the page and then choose *Website*.
![Image](~/images/cognitoHostedUI/facebook4.png)
5. Under Website, type your user pool domain with the /oauth2/idpresponse endpoint into *Site URL*

    ```https://<your-user-pool-domain>/oauth2/idpresponse```

    ![Image](~/images/cognitoHostedUI/facebook5.png)
6. Save changes.
7. Type your user pool domain into *App Domains*:

    ```https://<your-user-pool-domain>```
    
    ![Image](~/images/cognitoHostedUI/facebook6.png)
8. Save changes.
9. From the navigation bar choose *Products* and then *Set up* from *Facebook Login*.
![Image](~/images/cognitoHostedUI/facebook7.png)
10. From the navigation bar choose *Facebook Login* and then *Settings*.
11. Type your redirect URL into *Valid OAuth Redirect URIs*. It will consist of your user pool domain with the /oauth2/idpresponse endpoint.

    ```https://<your-user-pool-domain>/oauth2/idpresponse```

    ![Image](~/images/cognitoHostedUI/facebook8.png)
12. Save changes.

### Setting up Hosted UI Domain with Google

1. Go to [Google Developer Console](https://developers.google.com/identity/sign-in/web/sign-in)
2. Click *CONFIGURURE A PROJECT*
![Image](~/images/cognitoHostedUI/google1.png)
3. Type in a project name and choose *NEXT*.
![Image](~/images/cognitoHostedUI/google2.png)
4. Type in your product name and choose *NEXT*.
5. Choose *Web browser* from the *Where are you calling from?* drop-down list.
![Image](~/images/cognitoHostedUI/google3.png)
6. Click *CREATE*. You will NOT use the *Client ID* and *CLient Secret* from this step.
7. Click Done.
8. Go to the [Google developer console](https://console.developers.google.com).
9. On the left navigation bar, choose *Credentials*.
![Image](~/images/cognitoHostedUI/google5.png)
10. Select the client you created in the first step and choose the edit option.
11. Type your user pool domain into Authorized Javascript origins.
12. Type your user pool domain with the `/oauth2/idpresponse` endpoint into *Authorized Redirect URIs*.

    ![Image](~/images/cognitoHostedUI/google7.png)

    Note: If you saw an error message `Invalid Redirect: domain must be added to the authorized domains list before submitting.` when adding the endpoint, please go to the *authorized domains list* and add the domain.
13. Click *Save*.

### Setting up Hosted UI Domain with Login with Amazon

1. [Sign in](https://developer.amazon.com/loginwithamazon/console/site/lwa/overview.html) with your Amazon credentials.
2. Hover over the gear and choose Web Settings associated with the security profile you created in the previous step, and then choose Edit.
![Image](~/images/cognitoHostedUI/amazon4.png)
3. Type your user pool domain into Allowed Origins and type your user pool domain with the /oauth2/idpresponse endpoint into Allowed Return URLs.
![Image](~/images/cognitoHostedUI/amazon5.png)
5. Choose Save.

### Manual Setup

To configure your application for hosted UI, you need to use *HostedUI* options. Update your `awsconfiguration.json` file to add a new configuration for `Auth`. The configuration should look like this:

```json
{
    "IdentityManager": {
        ...
    },
    "CredentialsProvider": {
        ...
    },
    "CognitoUserPool": {
        ...
    },
    "Auth": {
        "Default": {
            "OAuth": {
                "WebDomain": "YOUR_AUTH_DOMAIN.auth.us-west-2.amazoncognito.com", // Do not include the https:// prefix
                "AppClientId": "YOUR_APP_CLIENT_ID",
                "SignInRedirectURI": "myapp://callback",
                "SignOutRedirectURI": "myapp://signout",
                "Scopes": ["openid", "email"]
            }
        }
    }
}
```

Note: The User Pool OIDC JWT token obtained from a successful sign-in will be federated into a configured Cognito Identity pool in the `awsconfiguration.json` and the SDK will automatically exchange this with Cognito Identity to also retrieve AWS credentials.

### Setup Amazon Cognito Hosted UI in Android App

1. Add `myapp://` to your app's Intent filters located in `AndroidManifest.xml`. The `your.package.YourAuthIntentHandlingActivity` will be referenced in the next step.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
     <manifest xmlns:android="http://schemas.android.com/apk/res/android"
               xmlns:amazon="http://schemas.amazon.com/apk/res/android"
               package="com.amazonaws.mobile.client">

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

        <application>
            <activity android:name="your.package.YourAuthIntentHandlingActivity">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />

                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />

                    <data android:scheme="myapp" />
                </intent-filter>
            </activity>
        </application>

    </manifest>
    ```

1. Attach an intent callback so that the AWSMobileClient can handle the callback and confirm sign-in or sign-out. This should be in `your.package.YourAuthIntentHandlingActivity`.

    ```java
    @Override
    protected void onResume() {
        super.onResume();
        Intent activityIntent = getIntent();
        if (activityIntent.getData() != null &&
                "myapp".equals(activityIntent.getData().getScheme())) {
            AWSMobileClient.getInstance().handleAuthResponse(activityIntent);
        }
    }
    ```

### Launching the Hosted UI

To launch the Hosted UI from from your application, you can use the `showSignIn` API of `AWSMobileClient.getInstance()`:

```java
// No options are being specified, only the config will be used
HostedUIOptions hostedUIOptions = HostedUIOptions.builder()
        .scopes("openid", "email")
        .build();
SignInUIOptions signInUIOptions = SignInUIOptions.builder()
        .hostedUIOptions(hostedUIOptions)
        .build();
// 'this' refers to the current active Activity
AWSMobileClient.getInstance().showSignIn(this, signInUIOptions, new Callback<UserStateDetails>() {
    @Override
    public void onResult(UserStateDetails details) {
        Log.d(TAG, "onResult: " + details.getUserState());
    }

    @Override
    public void onError(Exception e) {
        Log.e(TAG, "onError: ", e);
    }
});
```

**Note:** By default, the Hosted UI will show all sign-in options; the username-password flow as well as any social providers which are configured. If you wish to bypass the extra sign-in screen showing all the provider options and launch your desired social provider login directly, you can set the `HostedUIOptions` as shown in the next section.

### Configuring Hosted UI to launch Facebook/ Google/ SAML sign in directly

```java
// For Google
HostedUIOptions hostedUIOptions = HostedUIOptions.builder()
    .scopes("openid", "email")
    .identityProvider("Google")
    .build();

// For Facebook
HostedUIOptions hostedUIOptions = HostedUIOptions.builder()
    .scopes("openid", "email")
    .identityProvider("Facebook")
    .build();

SignInUIOptions signInUIOptions = SignInUIOptions.builder()
    .hostedUIOptions(hostedUIOptions)
    .build();
// 'this' refers to the current active Activity
AWSMobileClient.getInstance().showSignIn(this, signInUIOptions, new Callback<UserStateDetails>() {
    @Override
    public void onResult(UserStateDetails details) {
        Log.d(TAG, "onResult: " + details.getUserState());
    }

    @Override
    public void onError(Exception e) {
        Log.e(TAG, "onError: ", e);
    }
});
```

### Sign Out from HostedUI

```java
AWSMobileClient.getInstance().signOut(SignOutOptions.builder().invalidateTokens(true).build(), new Callback<Void>() {
    @Override
    public void onResult(Void result) {
        Log.d(TAG, "onResult: ");
    }

    @Override
    public void onError(Exception e) {
        Log.e(TAG, "onError: ", e);
    }
});
```

If you want to sign out locally by just deleting tokens, you can call `signOut` method:

```java
AWSMobileClient.getInstance().signOut();
```

## Using Auth0 Hosted UI

You can use `AWSMobileClient` to use `Auth0` as `OAuth 2.0`  provider.
You can use `Auth0` as one of the providers of your Cognito Federated Identity Pool.
This will allow users authenticated via Auth0 have access to your AWS resources. Learn [how to integrate Auth0 with Cognito Federated Identity Pools](https://auth0.com/docs/integrations/integrating-auth0-amazon-cognito-mobile-apps)

### Setup Auth0 Hosted UI in Android App

### Setup Amazon Cognito Hosted UI in Android App

1. To configure your application for hosted UI, you need to use *HostedUI* options. Update your `awsconfiguration.json` file to add a new configuration for `Auth`. The configuration should look like this:

    ```json
    {
        "IdentityManager": {
            ...
        },
        "CredentialsProvider": {
            ...
        },
        "CognitoUserPool": {
            ...
        },
        "Auth": {
            "Default": {
                "OAuth": {
                    "AppClientId": "YOUR_AUTH0_APP_CLIENT_ID",
                    "TokenURI": "https://YOUR_AUTH0_DOMAIN.auth0.com/oauth/token",
                    "SignInURI": "https://YOUR_AUTH0_DOMAIN.auth0.com/authorize",
                    "SignInRedirectURI": "com.your.bundle.configured.in.auth0://YOUR_AUTH0_DOMAIN.auth0.com/android/com.your.bundle/callback",
                    "SignOutURI": "https://YOUR_AUTH0_DOMAIN.auth0.com/v2/logout",
                    "SignOutRedirectURI": "com.your.bundle.configured.in.auth0://yourserver.auth0.com/android/com.amazonaws.AWSAuthSDKTestApp/signout",
                    "SignOutURIQueryParameters": {
                        "client_id" : "YOUR_AUTH0_APP_CLIENT_ID",
                        "returnTo" : "com.your.bundle.configured.in.auth0://yourserver.auth0.com/android/com.amazonaws.AWSAuthSDKTestApp/signout"
                    },
                    "Scopes": ["openid", "email"]
                }
            }
        }
    }
    ```

1. Add the sign-in and sign-out redirect URIs to your app's Intent filters located in `AndroidManifest.xml`.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
     <manifest xmlns:android="http://schemas.android.com/apk/res/android"
               xmlns:amazon="http://schemas.amazon.com/apk/res/android"
               package="com.amazonaws.mobile.client">

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

        <application>
            <activity android:name="your.package.YourAuthIntentHandlingActivity">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />

                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />

                    <data android:scheme="com.your.bundle.configured.in.auth0" />
                </intent-filter>
            </activity>
        </application>

    </manifest>
    ```

1. Attach an intent callback so that the AWSMobileClient can handle the callback and confirm sign-in or sign-out. This should be in `your.package.YourAuthIntentHandlingActivity`.

    ```java
    @Override
    protected void onResume() {
        super.onResume();
        Intent activityIntent = getIntent();
        if (activityIntent.getData() != null &&
                "myapp".equals(activityIntent.getData().getScheme())) {
            AWSMobileClient.getInstance().handleAuthResponse(activityIntent);
        }
    }
    ```

### Launching the Hosted UI for Auth0

To launch the Hosted UI from from your application, you can use the `showSignIn` API of `AWSMobileClient.getInstance()`:

```java
final HostedUIOptions hostedUIOptions = HostedUIOptions.builder()
        .federationProviderName("YOUR_AUTH0_DOMAIN.auth0.com")
        .build();
final SignInUIOptions signInUIOptions = SignInUIOptions.builder()
        .hostedUIOptions(hostedUIOptions)
        .build();
// 'this' refers to the current active Activity
AWSMobileClient.getInstance().showSignIn(this, signInUIOptions, new Callback<UserStateDetails>() {
    @Override
    public void onResult(UserStateDetails result) {
        Log.d(TAG, "onResult: " + result.getUserState());
    }

    @Override
    public void onError(Exception e) {
        Log.e(TAG, "onError: ", e);
    }
});
```

### Sign Out from HostedUI

```java
AWSMobileClient.getInstance().signOut(SignOutOptions.builder().invalidateTokens(true).build(), new Callback<Void>() {
    @Override
    public void onResult(Void result) {
        Log.d(TAG, "onResult: ");
    }

    @Override
    public void onError(Exception e) {
        Log.e(TAG, "onError: ", e);
    }
});
```

If you want to sign out locally by just deleting tokens, you can call `signOut` method:

```java
AWSMobileClient.getInstance().signOut();
```
