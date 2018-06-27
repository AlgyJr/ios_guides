### Overview

In the past, most iOS apps used custom URL scheme (i.e `fb://auth`).  The problem is that two apps can claim the same URL scheme and there is no way to protect against which app should launch.  Second, if the app was not installed, the URL doesn't open and there needs to be custom code to handle it.  Universal links try to solve both of these problems, first by requiring verifiable ownership and also provide graceful fallback.

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
                    "/videos/wwdc/2015/*/abcd"
                ]
            }
        }
    }
}
```

The `apple-app-site-association` paths don't support regular expressions, but can support wildcards in place of URL paths.

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

### References

* <https://objectivetidbits.com/working-with-universal-links-on-ios-simulator-adffb7767801>
* <https://developer.apple.com/ios/universal-links/>
* <https://medium.com/@abhimuralidharan/universal-links-in-ios-79c4ee038272/>
* <https://developer.apple.com/videos/play/wwdc2015/509/>
