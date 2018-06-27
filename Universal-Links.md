### Overview

In the past, most iOS apps used custom URL scheme (i.e `fb://auth`).  The problem is that two apps can claim the same URL scheme and there is no way to protect against which app should launch.  Second, if the app was not installed, the URL doesn't open and there needs to be custom code to handle it.  Universal links try to solve both of these problems, first by requiring verifiable ownership and also providing graceful fallback mechanism.

### Setup

First, register your app at `developer.apple.com` and make sure the `Associated Domains` is enabled for the app.

<img src="https://cdn-images-1.medium.com/max/2000/1*1APbM4gmiSnVg84uATVcdg.png"/>

Inside your XCode project, add the associated link in the format `applinks:my.example.com`:

<img src="https://cdn-images-1.medium.com/max/2000/1*gpKxDQbEYD9QrPutkpsEgw.png"/>

The final step requires hosting an `apple-app-site-association` file where the appID corresponds to your app’s Team ID (visit https://developer.apple.com/account/#/membership/ to get the teamID) and the Bundle Identifier. In the example above, `9JA89QQLNQ` is the Team ID and `com.apple.wwdc` is the Bundle ID.

```javascript
{
    "applinks": {
        "apps": [],
        "details": {
            "9JA89QQLNQ.com.apple.wwdc": {
                "paths": [
                    "/wwdc/news/",
                    "/videos/wwdc/2015/*/highlights/"
                ]
            }
        }
    }
}
```

The `apple-app-site-association` paths don't support regular expressions, but can support wildcards in place of URL paths.

If your AppDelegate.swift,

```swift
func application(_ application: UIApplication, continue userActivity: NSUserActivity, restorationHandler: @escaping ([Any]?) -> Void) -> Bool {

 // /videos/wwdc/2015/*/highlights/
 if let regex = try? NSRegularExpression(pattern: "/videos/wwdc/(?<yearId>\\d+)/(.*?)/highlights/?", options: .caseInsensitive) {
  if let matches = regex.matches(in: url.absoluteString, options: [], range: NSMakeRange(0, url.absoluteString.count)) {
     return true
   }
}

```

### Wildcard Links

To use wildcard app links, you must make sure that the subdomain and sub subdomains can serve the `apple-app-site-association` file.    For instance:

* app.domain.com (must serve the apple-app-site-association file)
* server1.app.domain.com (must serve the apple-app-site-association)
* server2.app.domain.com (must serve the apple-app-site-association)

### Troubleshooting

Use the Console app, click on the Devices, and look for `applinks` in the system messages.

### Testing on Simulator

Click on the Home button and make sure the app is currently not in the foreground.  To launch the simulator, you can type this command at the Terminal prompt:

```bash
xcrun simctl openurl booted “https://www.example.com/content?id=2”
```

### Limitations

Keep in mind that Safari will not attempt to launch the app if it is already on the same domain according to [Apple's documentation](https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html):

_When a user is browsing your website in Safari and they tap a universal link to a URL in the same domain as the current webpage, iOS respects the user’s most likely intent and opens the link in Safari. If the user taps a universal link to a URL in a different domain, iOS opens the link in your app._

_For users who are running versions of iOS earlier than 9.0, tapping a universal link to your website opens the link in Safari._

You will need to either expose a url with a different domain (i.e. deeplink.example.com) or use a third-party service like [branch.io](http://branch.io), which supports custom domains.

### References

* <https://objectivetidbits.com/working-with-universal-links-on-ios-simulator-adffb7767801>
* <https://developer.apple.com/ios/universal-links/>
* <https://medium.com/@abhimuralidharan/universal-links-in-ios-79c4ee038272/>
* <https://developer.apple.com/videos/play/wwdc2015/509/>
