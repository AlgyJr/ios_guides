## 1. Overview

Parse provides a cloud-based backend service to build data-driven mobile apps quickly.  Facebook, which [acquired the company](https://en.wikipedia.org/wiki/Parse_(company)) in 2013, shut down service on January 28, 2017. An [open source version](https://github.com/ParsePlatform/parse-server) enables developers to continue using their apps was published, along with a [migration guide](https://github.com/ParsePlatform/parse-server#migrating-an-existing-parse-app).

While there are many
[alternate options to Parse](https://github.com/relatedcode/ParseAlternatives), most of them lack either the functionality, documentation, or sample code to enable quick prototyping.  For this reason, the open source Parse version is a good option to use with minimal deployment/configuration needed.

### 1.1 Differences with Open Source Parse

You can review this [Wiki](https://github.com/ParsePlatform/parse-server/wiki) to understand the current development progress of this app.  There are a few notable differences in the open source version:

* **Authentication**: By default, only an application ID is needed to authenticate with open source Parse.  The [base configuration](https://github.com/ParsePlatform/parse-server-example/blob/master/index.js#L13-L18) that comes with the one-click deploy options does not require authenticating with any other types of keys.   Therefore, specifying client keys on Android or iOS is not needed.

* **Push notifications**: Because of the implicit [security issues](https://github.com/ParsePlatform/parse-server/issues/396#issuecomment-183792657) with allowing push notifications to be sent through Android or iOS directly to other devices, this feature is disabled.  Normally in Parse.com you can toggle an option to override this security restriction.  For open source Parse, you must implement pre-defined code written in JavaScript that can be called by the clients to execute, otherwise known as [Parse Cloud]( http://blog.parse.com/announcements/pushing-from-the-javascript-sdk-and-cloud-code/).

* **Single app aware**: The current version only supports single app instances.  There is ongoing work to make this version multi-app aware.  However, if you intend to run many different apps with different datastores, you currently would need to instantiate separate instances.

* **File upload limitations**: The backend for open source is backed by MongoDB, and the default storage layer relies on Mongo's GridFS layer.  The current limit is set for 20 MB but you depend on storing large files, you should really configure the server to use Amazon's Simple Storage Service (S3).

Many of the options need to be configured by tweaking your own configuration.  You may wish to fork the [code](https://github.com/ParsePlatform/parse-server-example/) that helps instantiate a Parse server and change them based on your own needs.   

## 2. Setting a new Parse Server

The steps described in [this guide](https://devcenter.heroku.com/articles/deploying-a-parse-server-to-heroku) walk through most of the process of setting an open source version with Parse. There are obviously many other hosting options, but the one-click deploy made available with Heroku as discussed in [this guide](https://github.com/ParsePlatform/parse-server-example) is the simplest. **Note:** You are likely to need a credit card attached to your account to activate.

### 2.1 Signing up with Heroku

Use Heroku if you have little or no experience with setting up web sites. Heroku allows you to manage changes to deploy easily by specifying a GitHub repository to use.  In addition, it comes with a UI data viewer from MongoLabs.  

1. Sign Up / Sign In at [Heroku](https://www.heroku.com)

2. Click on the button below to create a Parse App

      <a href="https://dashboard.heroku.com/new?button-url=https%3A%2F%2Fgithub.com%2FParsePlatform%2Fparse-server-example&template=https%3A%2F%2Fgithub.com%2FParsePlatform%2Fparse-server-example"><img src="https://camo.githubusercontent.com/c0824806f5221ebb7d25e559568582dd39dd1170/68747470733a2f2f7777772e6865726f6b7563646e2e636f6d2f6465706c6f792f627574746f6e2e706e67" alt="Deploy" data-canonical-src="https://www.herokucdn.com/deploy/button.png" style="max-width:100%;"></a>

2. Make sure to enter an App Name.  Scroll to the bottom of the page.

      <img src="https://imgur.com/0JcJrn5.png">

3. Make sure to change the config variables.
      <img src="https://i.imgur.com/dArCPmZ.png"/>
      * Leave `PARSE_MOUNT` to be `/parse`.  It does not need to be changed.
      * Set `APP_ID` for the app identifier.  If you do not set one, the default is set as `myAppId`.  You will need this info for the Client SDK setup.
      * Set `MASTER_KEY` to be the master key used to read/write all data.  You will only use this key if you intend to setup the Parse Dashboard. 
      * Set `SERVER_URL` to correspond to match the App Name you defined in step #2 along with the PARSE_MOUNT  (e.g. https://yourappname.herokuapp.com/parse)
      * If you intend to use Parse's Facebook authentication, set `FACEBOOK_APP_ID` to be the [FB application ID](https://developers.facebook.com/apps).

4. Deploy the Heroku app.  The app should be hosted at `https://<app name>.herokuapp.com`.

If you ever need to change these values later, you can go to (`https://dashboard.heroku.com/apps/<app name>/settings`).

### 2.2 Testing Deployment

After deployment, open the url (`https://<app name>.herokuapp.com`) in a browser.  You should see `I dream of being a web site.` if the site loaded correctly.   If you try to open `https://<app name>.herokuapp.com/parse`, you should see `{error: "unauthorized"}`.  If both tests pass, the basic configuration is successful.

Next, make sure you can create Parse objects.  You do not need a client Key to write new data:

```bash
curl -X POST -H "X-Parse-Application-Id: myAppId" -H "X-Parse-Master-Key: abc" \
-H "Content-Type: application/json" \
-d '{"score":1337,"playerName":"Sean Plott","cheatMode":false}' \
https://yourappname.herokuapp.com/parse/classes/GameScore
```

Be sure to **replace the values** for `myAppId` and the server URL. If you see `Cannot POST` error then be sure both the `X-Parse-Application-Id` and the URL are correct for your application. To read data back, you will need to specify your master key as well:

```bash
curl -X GET -H "X-Parse-Application-Id: myAppId" -H "X-Parse-Master-Key: abc"  \
https://yourappname.herokuapp.com/parse/classes/GameScore
```

Be sure to **replace the values** for `myAppId` and the server URL. If these commands work as expected, then your Parse instance is now setup and ready to be used!

## 3. Browsing Parse Data

There are several options that allow you to view the data.  First, you can use the `mLab` viewer to examine the store data.  Second, you can setup the open source verson of the Parse Dashboard, which gives you a similar UI used in hosted Parse.  Finally, you can use Robomongo.

### 3.1 mLab

The hosted Parse instance deployed uses [mLab](https://mlab.com/) (previously called MongoLab) to store all of your data. mLab is a hosted version of [MongoDB](https://www.mongodb.org/) which is a document-store which uses JSON to store your data.

If you are using Heroku, you can verify whether the objects were created by clicking on the MongoDB instance in the Heroku panel:

<img src="https://imgur.com/bbj2e9N.png"/>

<img src="https://imgur.com/snPqYkz.png"/>

### 3.2 Parse Dashboard

You can also install Parse's open source dashboard locally. Download [NodeJS v4.3](https://nodejs.org/en/download/) or higher.  Make sure you have at least Parse server v2.1.3 or higher (later versions include a `/parse/serverInfo` that is needed).

```bash
npm install -g parse-dashboard 
parse-dashboard --appId myAppId --masterKey myMasterKey --serverURL "https://yourapp.herokuapp.com/parse"
```
Note: If you get an error regarding **access permissions**, add `sudo` in front of your command to execute with root privileges:

```bash
sudo npm install -g parse-dashboard
```
Connect to your dashboard at `http://localhost:4040/apps`. Assuming you have specified the correct application ID, master Key, and server URL, as well as installed a Parse open source version v2.1.3 or higher, you should see the app appear correctly:

<img src="https://imgur.com/Z0Rz5Xs.png"/>

### 3.3 Robomongo

You can also setup [Robomongo](https://robomongo.org/download) to connect to your remote mongo database hosted on Heroku to get a better data browser and dashboard for your app.

<a href="https://robomongo.org/download"><img src="https://i.imgur.com/9Qtt6Xs.png" width="450" /></a>

To access mLab databases using Robomongo, be sure to go the MongoDB instance in the Heroku panel as shown above. Look for the following URL: `mongodb://<dbuser>:<dbpassword>@ds017212.mlab.com:11218/heroku_2flx41aa`. Use that to identify the login credentials:

```
address: ds017212.mlab.com
port: 11218
db: heroku_2flx41aa
user: dbuser
password: dbpassword
```

Using that cross-platform app to easily access and modify the data for your Parse MongoDB data. 

## 4. Add Parse client to an Xcode project

1. Create a Podfile file:

    ```
    pod init
    ```
1. Add dependencies in your Podfile (Don't forget to save your Podfile):

    ```ruby
    # Uncomment the next line to define a global platform for your project
    # platform :ios, '9.0'

    target 'YOUR_APP' do
      # Comment the next line if you're not using Swift and don't want to use dynamic frameworks
      use_frameworks!

      # Pods for YOUR_APP
      pod 'Parse'

      ...
    ```

1. Install the new pods:

    ```bash
    pod install
    ```

1. Initialize Parse in your `AppDelegate` to point to your own server:

    ```swift
    // AppDelegate.swift

    // ...

    import Parse

    class AppDelegate: UIResponder, UIApplicationDelegate {

        // ...

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {

            // Initialize Parse
            // Set applicationId and server based on the values in the Heroku settings.
            Parse.initialize(
                with: ParseClientConfiguration(block: { (configuration: ParseMutableClientConfiguration) -> Void in
                    configuration.applicationId = "myAppId"
                    configuration.server = "https://myAppName.herokuapp.com/parse"
                })
            )
    ```
    ```objc
    #import "AppDelegate.h"
    #import "Parse/Parse.h"
  
    @interface AppDelegate ()
  
    @end
  
    @implementation AppDelegate
  
  
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(  NSDictionary *)launchOptions {
        
        ParseClientConfiguration *config = [ParseClientConfiguration   configurationWithBlock:^(id<ParseMutableClientConfiguration> configuration) {
            
            configuration.applicationId = @"codepathInstagram";
            configuration.server = @"http://codepathfbinstagram.herokuapp.com/parse";
        }];
        
        [Parse initializeWithConfiguration:config];
        
        return YES;
    }
    ```

The `/parse` path needs to match the `PARSE_MOUNT` environment variable, which is set to this value by default.

## 5. Adding Support for Live Queries

One of the newer features of Parse is that you can monitor for live changes made to objects in your database (i.e. creation, updates, and deletes)  To get started, make sure you have defined the ParseObjects that you want in your NodeJS server.  Make sure to define a list of all the objects by declaring it in the `liveQuery` and `classNames listing`:

```javascript
let api = new ParseServer({
  ...,
  // Make sure to define liveQuery AND classNames
  liveQuery: {
    // define your ParseObject names here
    classNames: ['Post', 'Comment']
  }
});
```

See [this guide](http://parseplatform.org/docs/parse-server/guide/#live-queries) and [this spec](https://github.com/parse-community/parse-server/wiki/Parse-LiveQuery-Protocol-Specification) for more details.  Parse Live Queries rely on the websocket protocol, which creates a bidirectional channel between the client and server and periodically exchange ping/pong frames to validate the connection is still alive.

Websocket URLs are usually prefixed with ws:// or wss:// (secure) URLs.  Heroku instances already provide websocket support, but if you are deploying to a different server (Amazon), you may need to make sure that TCP port 80 or TCP port 443 are available.

## 6. Troubleshooting

* If you see `Application Error` or `An error occurred in the application and your page could not be served. Please try again in a few moments.`, double-check that you set a `MASTER_KEY` in the environment settings for that app.

  <img src="https://imgur.com/uMYwPmS.png">

* If you are using Heroku, download the Heroku Toolbelt app [here](https://toolbelt.heroku.com/) to help view system logs.

  <img src="https://imgur.com/Ch0mZOK.png"/>

  First, you must login with your Heroku login and password:

  ```bash
  heroku login
  ```

   You can then view the system logs by specifying the app name:
   ```bash
   heroku logs --app <app name>
   ```

   The logs should show the response from any types of network requests made to the site.  Check the `status` code.
   ```
   2016-02-07T08:28:14.292475+00:00 heroku[router]: at=info method=POST path="/parse/classes/Message" host=parse-testing-port.herokuapp.com request_id=804c2533-ac56-4107-ad05-962d287537e9 fwd="101.12.34.12" dyno=web.1 connect=1ms service=2ms status=404 bytes=179
   ```

## 7. Enabling Push Notifications

1. Create an auth token through [developer.apple.com](http://developer.apple.com) by clicking on the `Keys` -> `All` section.  Fill out a name and make sure the APNS service is **checked**:
      <img src="https://imgur.com/MfQWtqy.png" width="700">
   Save the `.p8` file and record the key ID.  You will need to add this information to your Parse configuration.

2. Fork your own [copy](https://github.com/ParsePlatform/parse-server-example) of the Parse server code that initially used to deploy to Heroku.  You will need to reconfigure your Heroku instance to point to this repo instead of Parse's because of additional customizations needed to be made on the `index.js` file within this repo.  

3. Copy the `.p8` certificate you exported and add it to this forked repo.  This `.p8` file should not have a passphrase with it.  

4. You will now need to edit the `index.js` to include to the APNS certificate.  You will need to specify the key ID, team ID, and the location of this `p8` certificate.
       ```javascript
       var authKeyPath = path.resolve(__dirname, 'AuthKey.p8');

       var pushConfig = {'ios': { token: {
          key: authKeyPath, // P8 file only
          keyId: 'XXXXX', // key ID
          teamId: 'YYYYY', // The Team ID of your Apple Developer Account (available at https://developer.apple.com/account/#/membership/)
         },
         production: false // set explicitly
        }
       };
       ```

    The Parse server relies on the [node-apn](https://github.com/node-apn/) module for sending Apple push notifications.  See [this guide](https://github.com/node-apn/node-apn/blob/master/doc/provider.markdown) for more information about iOS push options.  

5. Make sure to include this `pushConfig` into your definition:

       ```javascript
           var api = new ParseServer({
           .
           .
           push: pushConfig,
           });
       ```

6. Follow [client steps](http://docs.parseplatform.org/parse-server/guide/#configuring-your-clients-to-receive-push-notifications) to enable Push notifications inside your app.  
     * Make sure to use the same bundle identifier as the name specified in your server configuration.
     * Verify that you've turned on Push Notifications in the `Capabilities` section.
     * Click on `Build Setting`", and find (or search for) the `Code Signing Identity` field. This field should be set to `iOS Developer` if you're testing against development, or `iOS Distribution` if you're testing in production or building your app for the App Store.

7. Make sure to register your application for push notifications.  First, you should specify inside `AppDelegate.swift` the notification types to which the app will respond:

       ```swift
       // Swift
       func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
        {
          ...
          let userNotificationTypes: UIUserNotificationType = [.Alert, .Badge, .Sound]
          let settings = UIUserNotificationSettings(forTypes: userNotificationTypes, categories: nil)
          application.registerUserNotificationSettings(settings)
          application.registerForRemoteNotifications()
           ...
        }
       ```

8. Next, the `application:didRegisterForRemoteNotificationsWithDeviceToken:` will be called if registration is successful.  The response comes with a device token which we want to pass along to the server.

      ```swift
      func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData) {
          let installation = PFInstallation.currentInstallation()
          installation.setDeviceTokenFromData(deviceToken)
          installation.channels = ["global"]
          installation.saveInBackground()
      }
      ```

9. Test out whether you can receive push notifications by using this Curl command:

      ```bash
      curl -X POST \
      -H "X-Parse-Application-Id: myAppId" \
      -H "X-Parse-Master-Key: masterKey" \
      -H "Content-Type: application/json" \
      -d '{
            "where": {
              "deviceType": "ios"
            },
            "data": {
              "title": "The Shining",
              "alert": "All work and no play makes Jack a dull boy."
            }
          }'\   http://yourherouapp.herokuapp.com/parse/push
      ```

      You should see inside your logs:

      ```
      APNS Connection 0 Connected
      APNS Connection 0 Notification transmitted to <device_token>
      ```

NOTE: Apple has two separate push notification environments for production and development purposes.  Whether your push tokens are granted for development or production purposes depends on the certificate used to build your app.  If the app was signed using an App Store certificate, it is designated for production.  Otherwise, in most other cases, the token is relying on the development environment.

If you are building and testing your app in XCode (note that push notifications cannot be tested on an emulator), you will likely be testing in the development environment.  Once your app is distributed through the app store, you will need to setup the Parse server to be rely on the `production: true` setting.  You will most likely need to have separate Parse servers, one setup for production and the other setup for development purposes.

#### Sending Pushes from Clients

While support for push notifications is now available with the open source Parse server, **you cannot implement this type of code** on the actual client:   

```swift
   // Note: This does NOT work with open Parse Server at this time
   let push = PFPush.init()
   push.setChannel("mychannel")
   push.setMessage("this is my message")
   push.sendPushInBackground()
```

You will likely see this error in the API response:

```json
unauthorized: master key is required (Code: 0, Version: 1.12.0)
```

Instead, you need to write your own server-side Parse code and have the client invoke it.   

Verify that `cloud/main.js` is the default value of `CLOUD_CODE_MAIN` environment variable.    You should modify your `cloud/main.js` file to define this Parse Cloud function:

```javascript
// iOS push testing
Parse.Cloud.define("iosPushTest", function(request, response) {

  // request has 2 parameters: params passed by the client and the authorized user                                                                                                                               
  var params = request.params;
  var user = request.user;

  // Our "Message" class has a "text" key with the body of the message itself                                                                                                                                    
  var messageText = params.text;

  var pushQuery = new Parse.Query(Parse.Installation);
  pushQuery.equalTo('deviceType', 'ios'); // targeting iOS devices only                                                                                                                                          

  Parse.Push.send({
    where: pushQuery, // Set our Installation query                                                                                                                                                              
    data: {
      alert: "Message: " + messageText
    }
  }, { success: function() {
      console.log("#### PUSH OK");
  }, error: function(error) {
      console.log("#### PUSH ERROR" + error.message);
  }, useMasterKey: true});

  response.success('success');
});
```

Make sure to redeploy your code with these changes to Heroku first.  Then you can use the client to test:

```bash
curl -X POST \
-H "X-Parse-Application-Id: myAppId" \
-H "X-Parse-Master-Key: masterKey" \
-H "Content-Type: application/json" \
-d '{
      "where": {
        "deviceType": "ios"
      },
      "text": "This is a test"
    }' \
http://myherokuapp.herokuapp.com/parse/functions/iosPushTest
```

You should receive a `{"result":"success"}` message back if your `application ID` and `masterKey` matches your configuration.

You can then invoke this function inside your iOS client by adding the following command.  Note how the `text` parameter is used for the message to be sent:

```swift
PFCloud.callFunctionInBackground("iosPushTest", withParameters: ["text" : "Testing"])
```

### Troubleshooting

* Query the `Installation` table and make sure your app has registered a device token.   

  ```bash
  curl -X GET \
       -H "X-Parse-Application-Id: myAppId" \
       -H "X-Parse-Master-Key: masterKey" \
       http://myappname.herokuapp.com/parse/installations | python -mjson.tool
  ```

  You should see:

  ```
    {
            "appIdentifier": "beta.codepath.pushtest",
            "appName": "pushtest",
            "appVersion": "1",
            "badge": 0,
            "channels": [
                "global"
            ],
            "createdAt": "2016-03-13T07:07:12.184Z",
            "deviceToken": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
            "deviceType": "ios",
            "installationId": "498afd36-1987-4762-a5a3-9b958de41089",
            "localeIdentifier": "en-US",
            "objectId": "E6RQBu4q3e",
            "parseVersion": "1.12.0",
            "timeZone": "America/Los_Angeles",
            "updatedAt": "2016-03-13T07:07:12.184Z"
        }
   ```

* Make sure your bundle ID matches what you specified in your `index.js`.  If you get `Invalid Token` responses, it means that you may have a mismatch issue.

* If you are using a development certificate, make sure it is marked as `production: false` in your Parse server configuration.

* Verify you can connect to Apple's APNS service by following [these instructions](https://github.com/argon/node-apn/wiki/Preparing-Certificates).

* Enable network logging on your IOS client by reviewing [this Parse guide](https://github.com/ParsePlatform/Parse-SDK-iOS-OSX/wiki/Network-Debug-Tool).
