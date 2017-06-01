# Introduction

In iOS 9, Apple introduced "App Transport Security," or ATS. This defaults apps to requiring an HTTPS connection, and returning an error for non-HTTPS connections.  In addition, HTTPS connections must also be using the latest protocol, Transport Layer Security (TLS) v1.2 and will fail to establish a connection if an older version is being used by the web server.

With modern web services, there is no reason to send data in the clear. Thanks to SSL session reuse, performance should no longer be a concern. All data should be sent over SSL.

These new defaults are useful for countering "leaks." While your may have moved all your REST API endpoints to HTTPS, they may reference insecure resources, such as image assets.

## Overriding SSL Requirement

Unfortunately, you may have to connect to APIs outside of your control which do not offer HTTPS.

You can poke holes in ATS by adding a `NSAppTransportSecurity` dictionary to Info.plist. Add an `NSExceptionDomains` dictionary to whitelist specific domains. It may resemble:

* `NSAppTransportSecurity`
	* `NSExceptionDomains`
		* api.rottentomatoes.com
			* `NSExceptionAllowsInsecureHTTPLoads`: YES

You may also use `NSAllowsArbitraryLoads` to completely disable ATS in your app:

![Imgur](http://i.imgur.com/Tmy1wYL.png)

Or in XML:
```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key><true/>
</dict>
```

**This is strongly discouraged.** Only use this during development.

### Testing

You can test out issues with App Transport Security by using the `nscurl` command:

```bash
nscurl --ats-diagnostics <URL>
```

The results will show you whether default connections will fail, and whether using using older versions of Transport Layer Security (TLS) or disabling options such as Perfect Forward Secrecy (PFS) will resolve the issue.  For instance, you may need to modify `Info.plist` to include downgrading TLS versions or disabling PFS options:

```xml
<key>mywebsite.com</key>
<dict>
   <key>NSExceptionAllowsInsecureHTTPLoads</key>
   <false/>
   <key>NSTemporaryExceptionMinimumTLSVersion</key>
   <string>1.0</string>
   <key>NSTemporaryExceptionRequiresForwardSecrecy</key>
   <false/>
</dict>
```

### Read More

[Working with Apple's App Transport Security](http://www.neglectedpotential.com/2015/06/working-with-apples-application-transport-security/)

If you need to troubleshoot, read [Apple's docs](https://developer.apple.com/library/content/qa/qa1887/_index.html) for more information how to debug any SSL-related issues.  Here are the [technical docs](https://developer.apple.com/library/content/technotes/tn2232/_index.html) for any specific error codes you may see in logs.