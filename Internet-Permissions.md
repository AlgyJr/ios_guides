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

### Read More

[Working with Apple's App Transport Security](http://www.neglectedpotential.com/2015/06/working-with-apples-application-transport-security/)