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


### Adding Other Exceptions

Starting in iOS 10, Apple provided a few `.plist` keys that can be used to narrow down the exceptions you include to ATS's strict security. Instead of using `NSAllowsArbitraryLoads` by itself, you can also now use a few keys that override this key's behavior. Quoting the [Apple doc](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW60) on the topic: 
> In iOS 10 and later, and macOS 10.12 and later, the value of [`NSAllowsArbitraryLoads`] is ignored—resulting in an effective value for this key of its default value of NO—if any of the following keys are [also] present in your app’s Info.plist file:
> * NSAllowsArbitraryLoadsForMedia
> * NSAllowsArbitraryLoadsInWebContent
> * NSAllowsLocalNetworking

Here's a quick table of what each key is used for. The [Apple doc](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW60) has more complete information on each key, if you need more information.

| Key | Usage | Requires Justification |
|------|-------|-|
| `NSAllowsArbitraryLoadsForMedia` | An optional Boolean value that, when set to `YES`, disables all App Transport Security restrictions for media loaded using APIs from the AV Foundation framework. | Yes |
| `NSAllowsArbitraryLoadsInWebContent` | Set this key’s value to `YES` to obtain exemption from ATS policies in your app’s web views (`WKWebView`s and `UIWebView`s), without affecting the ATS-mandated security of your NSURLSession connections. | Yes |
| `NSAllowsLocalNetworking` | An optional Boolean value that, when set to `YES`, removes App Transport Security protections for connections to unqualified domains and to `.local` domains, without disabling ATS for the rest of your app. | No |

> The keys that require justification will trigger an App Store review. The [Apple doc](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW60) lists some valid reasons you can submit, just search for `justification` on the doc - if you're using third party SDKs, one valid reason is that your app "must connect to a server managed by another entity that does not support secure connections".

Using any of these keys will effectively cancel out the blanket effects of the `NSAllowsArbitraryLoads` key on iOS 10+ devices. This seems great, since many users of these special keys may *only* want exceptions for downloaded videos, user-controlled web browsing, advertisement browser redirects, or other use cases. However, users of multiple third-party SDKs should be careful: If any third-party SDK requests that you use the `NSAllowsArbitraryLoads` key **by itself**, then you can't include any of these more specific keys in your `.plist`. Otherwise, you may break some functionality in the SDK that required the `NSAllowsArbitraryLoads` key by itself, since it may be relying on the effects of that key outside of web views, media playback, and/or local networking.

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

### Further Troubleshooting

Troubleshooting ATS-related issues can be difficult to do.  If you need to analyze the network traffic, you can follow Apple's guides to run [packet traces](https://developer.apple.com/library/content/qa/qa1176/_index.html#//apple_ref/doc/uid/DTS10001707-CH1-SECIOSPACKETTRACING) using a tool such as [Wireshark](https://www.wireshark.org/).  You can use these tools to understand the TLS handshake.

First, connect an iPhone to the USB port of a Mac.  Next, get the current list of interfaces:
```bash
$ ifconfig -l
lo0 gif0 stf0 en0 en1 p2p0 fw0 ppp0 utun0
```

Open iTunes on the Mac.  Click on the icon for the iPhone device.

Click the Serial number text to display the UDID.  Record the UDID by using Ctrl-Click to copy the UDID.

<img src="http://imgur.com/MLWsrsl.png"/>

Then run the tool with the UDID of the device:

```bash
$ rvictl -s <UDID>
```

You should see:
Starting device <UDID [SUCCEEDED]
 
Get the list of interfaces again, and you can see the new virtual network interface, rvi0, added by the previous command:

```bash
$ ifconfig -l
lo0 gif0 stf0 en0 en1 p2p0 fw0 ppp0 utun0 rvi0
```

You can then use WireShark to capture network traffic on the `rvi0` interface.

### Encryption Ciphers

ATS requires strict encryption ciphers.  You can use the Chrome Security tab (`View` -> `Developer` -> `Developer Tools`) to check what encryption standard is used:

<img src="http://imgur.com/QblJSTn.png"/>

Make sure web sites must implement strong encryption ciphers that implement perfect forward secrecy.  Here is the current list that iOS10 devices will support (in order of preference):

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA

If you are trying to test an internal site, the `nmap` tool can also be used to extract the encryption ciphers that a web site supports.

```bash
brew install nmap
nmap --script ssl-enum-ciphers -p 443 <URL>
```

A report similar to the following will be generated, showing what TLS standards are supported and what encryption ciphers can be used.

```
Nmap scan report for www.cnn.com (151.101.193.67)
Host is up (0.0057s latency).
Other addresses for www.cnn.com (not scanned): 151.101.1.67 151.101.65.67 151.101.129.67 2a04:4e42::323 2a04:4e42:200::323 2a04:4e42:400::323 2a04:4e42:600::323
PORT    STATE SERVICE
443/tcp open  https
| ssl-enum-ciphers: 
|   TLSv1.0: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (secp256r1) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_3DES_EDE_CBC_SHA (rsa 2048) - C
|     compressors: 
|       NULL
|     cipher preference: server
|     warnings: 
|       64-bit block cipher 3DES vulnerable to SWEET32 attack
|   TLSv1.1: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (secp256r1) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_3DES_EDE_CBC_SHA (rsa 2048) - C
|     compressors: 
|       NULL
|     cipher preference: server
|     warnings: 
|       64-bit block cipher 3DES vulnerable to SWEET32 attack
|   TLSv1.2: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (secp256r1) - A
|       TLS_RSA_WITH_AES_128_GCM_SHA256 (rsa 2048) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_3DES_EDE_CBC_SHA (rsa 2048) - C
|     compressors: 
|       NULL
|     cipher preference: server
|     warnings: 
|       64-bit block cipher 3DES vulnerable to SWEET32 attack
|_  least strength: C

Nmap done: 1 IP address (1 host up) scanned in 0.97 seconds
```

In addition, there are public sites such as [https://apptransport.info](https://apptransport.info) and [ssllabs.com/ssltest/analyze.html](ssllabs.com/ssltest/analyze.html) that allow you to check whether public web sites are ATS-compatible.  For apptransportinfo, Simply add the URL at the end (i.e. https://apptransport.info/www.codepath.com) to run the test.   

### Read More

[Working with Apple's App Transport Security](http://www.neglectedpotential.com/2015/06/working-with-apples-application-transport-security/)

If you need to troubleshoot, read [Apple's docs](https://developer.apple.com/library/content/qa/qa1887/_index.html) for more information how to debug any SSL-related issues.  Here are the [technical docs](https://developer.apple.com/library/content/technotes/tn2232/_index.html) for any specific error codes you may see in logs.