### Overview

Parse provides a cloud-based backend service to build data-driven mobile apps quickly.  Facebook, which acquired the company more than 3 years ago, announced that the service would be shutting down on **January 28, 2017**.   An [open source version](https://github.com/ParsePlatform/parse-server) enables developers to continue using their apps was published, along with a [migration guide](https://parse.com/docs/server/guide#migrating).

While there are many
[alternate options to Parse](https://github.com/relatedcode/ParseAlternatives), most of them lack either the functionality, documentation, or sample code to enable quick prototyping.  For this reason, the open source Parse version is a good option to use with minimal deployment/configuration needed.

You can review this [Wiki](https://github.com/ParsePlatform/parse-server/wiki) to understand the current development progress of this app.  Push notifications, for instance, are currently not implemented but appears to be supported soon.

### Setting a new Parse Server

The steps described in [this guide](https://devcenter.heroku.com/articles/deploying-a-parse-server-to-heroku) walk through most of the process of setting an open source version with Parse. There are obviously many other hosting options, but the one-click deploy made available with Heroku as discussed in [this guide](https://github.com/ParsePlatform/parse-server-example) is the simplest. **Note:** You are likely to need a credit card attached to your account to activate.

#### Signing up with Heroku

Use Heroku if you have little or no experience with setting up web sites. Heroku allows you to manage changes to deploy easily by specifying a GitHub repository to use.  In addition, it comes with a UI data viewer from MongoLabs.  

1. Sign Up / Sign In at [Heroku](https://www.heroku.com)

2. Click on the button below to create a Parse App

      <a href="https://dashboard.heroku.com/new?button-url=https%3A%2F%2Fgithub.com%2FParsePlatform%2Fparse-server-example&template=https%3A%2F%2Fgithub.com%2FParsePlatform%2Fparse-server-example"><img src="https://camo.githubusercontent.com/c0824806f5221ebb7d25e559568582dd39dd1170/68747470733a2f2f7777772e6865726f6b7563646e2e636f6d2f6465706c6f792f627574746f6e2e706e67" alt="Deploy" data-canonical-src="https://www.herokucdn.com/deploy/button.png" style="max-width:100%;"></a>

2. Make sure to enter an App Name.  Scroll to the bottom of the page.

      <img src="http://imgur.com/0JcJrn5.png">

3. Make sure to change the config values.
      <img src="http://imgur.com/vQV0X0S.png"/>
      * Leave `PARSE_MOUNT` to be `/parse`.  It does not need to be changed.
      * Set `APP_ID` for the app identifier.  If you do not set one, the default is set as `myAppId`.  You will need this info for the Client SDK setup.
      * Set `MASTER_KEY` to be the master key used to read all data.  
      * If you intend to use Facebook authentication, set `FACEBOOK_APP_ID` to be the [FB application ID](https://developers.facebook.com/apps).
      
4. Deploy the Heroku app.  The app should be hosted at `https://<app name>.herokuapp.com`.

If you ever need to change these values later, you can go to (`https://dashboard.heroku.com/apps/<app name>/settings`).

### Testing Deployment

After deployment, try to connect to the site.  You should see `I dream of being a web site.` if the site loaded correctly.   If you try to connect to the `/parse` endpoint, you should see `{error: "unauthorized"}`.  If both tests pass, the basic configuration is successful.

Next, make sure you can create Parse objects.  You do not need a client Key to write new data:

```bash
curl -X POST -H "X-Parse-Application-Id: myAppId" -H "Content-Type: application/json" -d '{"score":1337,"playerName":"Sean Plott","cheatMode":false}'   https://yourappname.herokuapp.com/parse/classes/GameScore
```

To read data back, you will need to specify the master key:

```bash
curl -X GET -H "X-Parse-Application-Id: myAppId" -H "X-Parse-Master-Key: abc"    https://yourappname.herokuapp.com/parse/classes/GameScore
```

If you are using Heroku, You can also verify whether the objects were created by clicking on the MongoDB instance in the Heroku panel:

<img src="http://imgur.com/bbj2e9N.png"/>

<img src="http://imgur.com/snPqYkz.png"/>

### Enabling Client SDK integration

Make sure to pull in the latest Parse SDK in your `Podfile` (it should be at least `1.12.0`):

```ruby
# Podfile

pod 'Parse'
```

Initialize Parse in your `AppDelegate` to point to your own server:

```swift
// AppDelegate.swift

// ...

import Parse

class AppDelegate: UIResponder, UIApplicationDelegate {

    // ...

    func application(application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {

        // Initialize Parse
        // Set applicationId, clientKey, and server based on the values in the Heroku settings.
        Parse.initializeWithConfiguration(
            ParseClientConfiguration(block: { (configuration:ParseMutableClientConfiguration) -> Void in
                configuration.applicationId = "myAppId"
                configuration.clientKey = "myMasterKey"    
                configuration.server = "https://myAppName.herokuapp.com/parse"
            })
        )
```

The `/parse` path needs to match the `PARSE_MOUNT` environment variable, which is set to this value by default.

### Troubleshooting

* If you see `Application Error` or `An error occurred in the application and your page could not be served. Please try again in a few moments.`, double-check that you set a `MASTER_KEY` in the environment settings for that app.

  <img src="http://imgur.com/uMYwPmS.png">

* If you are using Heroku, download the Heroku Toolbelt app [here](https://toolbelt.heroku.com/) to help view system logs.

  <img src="http://imgur.com/Ch0mZOK.png"/>

  First, you must login with your Heroku login and password:

  ```bash
  heroku login
  ```

   You can then view the system logs by specifying the app name:
   ```bash
   heroku logs -app <app name>
   ```

   The logs should show the response from any types of network requests made to the site.  Check the `status` code.
   ```
   2016-02-07T08:28:14.292475+00:00 heroku[router]: at=info method=POST path="/parse/classes/Message" host=parse-testing-port.herokuapp.com request_id=804c2533-ac56-4107-ad05-962d287537e9 fwd="101.12.34.12" dyno=web.1 connect=1ms service=2ms status=404 bytes=179
   ```
