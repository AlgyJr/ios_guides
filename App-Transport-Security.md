# Introduction

In iOS 9, Apple introduced "App Transport Security," or ATS. This defaults apps to requiring an HTTPS connection, and returning an error for non-HTTPS connections.

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

### Read More

[Working with Apple's App Transport Security](http://www.neglectedpotential.com/2015/06/working-with-apples-application-transport-security/)