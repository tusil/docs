Setup instructions are provided for Android and iOS, and configuration for both platforms can be included on the same React Native project. 

## Requirements
1. In order to use Amazon Pinpoint you need to setup credentials (keys or certificates) for your targeted mobile platform, e.g. Android and/or iOS.
2. Testing Push Notifications requires a physical device, because simulators or emulators won't be able to handle push notifications.

<amplify-callout>

Push Notifications category is integrated with [AWS Amplify Analytics category](~/lib/analytics/getting-started.md) to be able to track notifications. Make sure that you have configured the Analytics category in your app before configuring the Push Notifications category.

</amplify-callout>

## Setup for Android

1. Make sure you have a [Firebase Project](https://console.firebase.google.com) and app setup. 

2. Get your push messaging credentials for Android in Firebase console. [Click here for instructions](~/sdk/push-notifications/setup-push-service.md?platform=android).

3. Create a native link on a React Native app:

    ```bash
    react-native init myapp
    cd myapp
    npm install aws-amplify && npm install @aws-amplify/pushnotification
    react-native link @aws-amplify/pushnotification
    react-native link amazon-cognito-identity-js # link if you need to Sign in into Cognito user pool
    ```

    That would install required npm modules and link React Native binaries.

    Please note that linking `aws-amplify-react-native` but not completing the rest of the configuration steps could break your build process. Please be sure that you have completed all the steps before you build your app.

4. Add your push messaging credentials (API key and Sender ID) with Amplify CLI by using the following commands:

    ```bash
    cd myapp
    amplify init
    amplify add notifications
    amplify push
    ```

    Choose *FCM* when promoted: 

    ```console
    ? Choose the push notification channel to enable.
    APNS
    ❯ FCM
    Email
    SMS
    ```

    The CLI will prompt for your *Firebase credentials*, enter them respectively.

    Alternatively, you can set up Android push notifications in Amazon Pinpoint Console. [Click here for instructions](https://docs.aws.amazon.com/pinpoint/latest/developerguide/mobile-push-android.html).

5. Enable your app in Firebase. To do that, follow those steps:

    <amplify-callout>
        If you don't have an existing Firebase project, you need to create one in order to continue.
    </amplify-callout>

    * Visit the [Firebase console](https://console.firebase.google.com), and click the Gear icon next to **Project Overview** and click **Project Settings**. 
    * Click **Add App**, if you have an existing app you can skip this step
    * Choose **Add Firebase to your Android App**
    * Add your package name i.e. **com.myProjectName** and click **Register App**
    * Download  *google-services.json* file and copy it under your `android/app` project folder.
    
    <amplify-callout warning>

    Make sure you have *google-services.json* file in place or you won't pass the build process.
    
    </amplify-callout>

6. Open *android/build.gradle* file and perform following edits:

    - Add *classpath 'com.google.gms:google-services:3.2.0'* in the `dependencies` under *buildscript*:
        
    ```gradle
        dependencies {
            classpath 'com.android.tools.build:gradle:2.2.3'
            classpath 'com.google.gms:google-services:3.2.0'  

            // NOTE: Do not place your application dependencies here; they belong
            // in the individual module build.gradle files
        }
    ```
    Also update maven `url` as the following under  *allprojects > repositories*. Revise *allprojects* to be:

    ```gradle
        allprojects {
            repositories {
                mavenLocal()
                jcenter()
                maven {
                    // All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
                    url "$rootDir/../node_modules/react-native/android"
                }
                maven {
                    url "https://maven.google.com"
                }
            }
        }
    ```

7. Open *android/app/build.gradle* and perform following edits:

    - Add firebase libs to the `dependencies` section:

    ```gradle
    dependencies {
        compile project(':@aws-amplify/pushnotification')
        ..
        ..
        ..
        compile 'com.google.firebase:firebase-core:12.0.1'
        compile 'com.google.firebase:firebase-messaging:12.0.1'
    }
    ```
    - Add following configuration to the bottom of the file:

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ``` 

8. Open *android/app/src/main/AndroidManifest.xml* file and add the following configuration into `application` element.

    ```xml
    <application ... >

        <!--[START Push notification config -->
            <!-- [START firebase_service] -->
            <service
                android:name="com.amazonaws.amplify.pushnotification.RNPushNotificationMessagingService">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT"/>
                </intent-filter>
            </service>
            <!-- [END firebase_service] -->
            <!-- [START firebase_iid_service] -->
            <service
                android:name="com.amazonaws.amplify.pushnotification.RNPushNotificationDeviceIDService">
                <intent-filter>
                    <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
                </intent-filter>
            </service>
            <receiver
                android:name="com.amazonaws.amplify.pushnotification.modules.RNPushNotificationBroadcastReceiver"
                android:exported="false" >
                <intent-filter>
                    <action android:name="com.amazonaws.amplify.pushnotification.NOTIFICATION_OPENED"/>
                </intent-filter>
            </receiver>
        <!-- [END Push notification config -->

    </application>
    ```

9. Configure Push Notifications category for your app as shown in [Configure your App](#configure-your-app) section.

10. Run your app with `yarn` or with an appropriate run command.

    ```bash
    npm start
    ```

## Setup for iOS

1. For setting up iOS push notifications, you need to download and install Xcode from [Apple Developer Center](https://developer.apple.com/xcode/).
2. Setup iOS Push Notifications and create a p12 certificate as instructed here in [Amazon Pinpoint Developer Guide](https://docs.aws.amazon.com/pinpoint/latest/developerguide/apns-setup.html).
 
3. Create a native link on a React Native app:

    ```javascript
    $ react-native init myapp
    $ cd myapp
    $ npm install
    $ npm install aws-amplify && npm install @aws-amplify/pushnotification
    $ react-native link @aws-amplify/pushnotification
    $ react-native link amazon-cognito-identity-js # link it if you need to Sign in into Cognito user pool
    ```

    <amplify-callout warning>

    Please note that linking `aws-amplify-react-native` but not completing the rest of the configuration steps could break your build process. Please be sure that you have completed all the steps before you build your app.
    
    </amplify-callout>

4. Enable notifications and add your p12 certificate with Amplify CLI by using the following commands:

    ```bash
    cd myapp
    amplify init
    amplify add notifications
    amplify push
    ```

    Choose *APNS* when promoted:

    ```console
    ? Choose the push notification channel to enable.
    > APNS
    FCM
    Email
    SMS
    ```

    The CLI will prompt for your *p12 certificate path*, enter it respectively.

5. Open *ios/myapp.xcodeproj* project file with Xcode.

6. Using Xcode, **manually link** the `PushNotificationIOS` library to your project. Please follow those steps in [React Native developer Documentation](https://facebook.github.io/react-native/docs/linking-libraries-ios.html#manual-linking) (Step 3 is not required)

7. Add the following code at the top on the file *AppDelegate.m*:

    ```
    #import <React/RCTPushNotificationManager.h>
    ```

8. And then in your `AppDelegate` implementation add the following code:

    ```c
    // Required to register for notifications
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
    {
    [RCTPushNotificationManager didRegisterUserNotificationSettings:notificationSettings];
    }
    // Required for the register event.
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
    [RCTPushNotificationManager didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
    }
    // Required for the notification event. You must call the completion handler after handling the remote notification.
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
                                                            fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
    [RCTPushNotificationManager didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
    }
    // Required for the registrationError event.
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
    {
    [RCTPushNotificationManager didFailToRegisterForRemoteNotificationsWithError:error];
    }
    // Required for the localNotification event.
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
    {
    [RCTPushNotificationManager didReceiveLocalNotification:notification];
    }
    ```

9. Update General App settings:

    - Make sure you have logged in with your Apple Developer account on Xcode
    - Set bundle identifier (with the one you create on your Apple Developer Account)
    - Unselect **Automatically manage signing** under **Signing** section
    - On Signing (Debug, Release) set the provisioning profile (created on your Apple Developer Account)
 
    *Following screencast shows the required app settings in Xcode:*
    <img src="~/images/identifiers.gif"/>

10. Setup capabilities on your App and enable **Push Notifications** and **Background Modes**. On Background Modes select **Remote notifications**.

    *Following screencast shows the required app settings in Xcode:*
    <img src="~/images/capabilities.gif" />

11. Configure Push Notification module for your app as shown in [Configure your App](#configure-your-app) section.

12. Run your app:

    - On Xcode, select your device and run it first using as *Executable appName.app*. This will install the App on your device but it won't run it.
    - Select **Ask on Launch** for *Executable* option on menu chain *Product > Schema > Edit Scheme > Run > Info*.
    - Click *Run* button and select your app from the list.
    - In case the build fails, try cleaning the project with *shift + command + k*.

    *Following screencast shows the required app settings in Xcode:*
    <img src="~/images/runningApp.gif" />

## Configure your App

The Push Notifications category is integrated with `Analytics` module to be able to track notifications. Make sure that you have configured the Analytics module in your app before configuring Push Notification module.  

<amplify-callout>

If you don't have Analytics already enabled, see our [Analytics Developer Guide](~/lib/analytics/getting-started.md) to add Analytics to your app.

</amplify-callout>

First, import `PushNotification` module and configure it with `PushNotification.configure()`.

```javascript
import { PushNotificationIOS } from 'react-native';
import Analytics from '@aws-amplify/analytics';
import PushNotification from '@aws-amplify/pushnotification';

// PushNotification need to work with Analytics
Analytics.configure({
    // You configuration will come here...
});

PushNotification.configure({
    appId: 'XXXXXXXXXXabcdefghij1234567890ab',
    requestIOSPermissions: false, // OPTIONAL, defaults to true
});
```

`requestIOSPermissions` is an optional boolean flag which specifies whether or not to automatically request push notifications permissions in iOS when calling `PushNotification.configure` for the first time. If not provided, it defaults to `true`. When set to `false`, you may later call the method `PushNotification.requestIOSPermissions` at the explicit point in your application flow when you want to prompt the user for permissions.

You can also use `aws-exports.js` file in case you have set up your backend with Amplify CLI.

```javascript
import { PushNotificationIOS } from 'react-native';
import Analytics from '@aws-amplify/analytics';
import PushNotification from '@aws-amplify/pushnotification';
import awsconfig from './aws-exports';

// PushNotification need to work with Analytics
Analytics.configure(awsconfig);

PushNotification.configure(awsconfig);
```
