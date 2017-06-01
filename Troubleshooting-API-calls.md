## Overview

While setting up breakpoints in your code is usually the first place to check, it often can be useful to see the network traffic exchanged between your iOS device and a server.  There are often things you want to check:

* Am I using the correct API key?
* Am I passing in the correct HTTP GET or POST parameters?
* Do I have the right URL pattern?
* What is the server error code?
* What is the server response?

You should first try to verify whether your API calls are correct by visiting [https://dhc.restlet.com/](https://dhc.restlet.com/).   You can also install a Chrome browser extension [Dev HTTP Client](https://chrome.google.com/webstore/detail/dhc-rest-client/aejoelaoggembcahagimdiliamlcdmfm) or any other available REST-based client tester. 

<img src="http://imgur.com/GfVK80o.png" width="600"/>

It is also often useful to monitor the network traffic to help diagnose these issues.  One way to do so is to setup an HTTP proxy that can intercept the network requests.  The process requires two parts: one on the PC that will act as the proxy, and the other on the iOS device.  

**Note**: one drawback of this approach is that if you are using a network where the IP address of your PC changes, you will need to re-update the proxy settings on the device each time.

## Setting up a Proxy

***On your PC***:

1. Download a 30-day trial of [Charles Proxy](https://www.charlesproxy.com/download/).  There are versions available for Windows, Mac OS, and Linux.

2. You will need to insert a special root certificate in order to inspect SSL traffic.  If you are running an iOS simulator, run Charles Proxy and navigate to `Help` -> `SSL Proxying` -> `Install Charles Root Certificate on iOS Simulators`.  If you are not running an iOS simulator, skip this step for now.  You will be asked to install the SSL cert on the phone later.

     <img src="http://imgur.com/Ac5QR0x.png"/>

3. SSL sites that must be inspected must be explicitly declared by going to `Proxy` -> `SSL Proxying Settings`.  Type the domain name and port 443 assuming the site is connecting to a standard SSL port.  You can also use wildcards (i.e. `*.myexample.com')

     <img src="http://imgur.com/YXTqq93.png"/>

4. Disable the proxy from being used on your desktop web browser by simply going to `Proxy` -> `Proxy Settings` and uncheck the `Enable macOS proxy` option:

     <img src="http://imgur.com/zzWkuEX.png"/>

5. Make sure to ascertain your IP address of the machine with the proxy installed.  You can go to the `Help` -> `Local IP Address` to find it:

<img src="http://imgur.com/AwbbEwA.png"/>

***On your phone:***

1. Go to the Settings app, tap Wi-Fi, find the network you are connected to and then tap it to configure the network. 

   <img src="http://imgur.com/8KUEzr1.png"/>

   Scroll down to the HTTP Proxy setting, tap Manual. 

   Enter the IP address of your computer running Charles in the Server field, and the port Charles is running on in the Port field (usually 8888). Leave Authentication set to Off.

2. If you are on an iOS device, open Safari and go to [http://www.charlesproxy.com/getssl](http://www.charlesproxy.com/getssl).  If you are using the simulator, the certificate should already be installed if you followed the previous steps.
  
3. Connect to any web site.  You need to go back to the PC to grant authorization for the device to connect:

   <img src="http://imgur.com/yuRmGRC.png">

4. Go to Settings and navigate to General > About > Certificate Trust Settings, and find the Charles Proxy certificate, and switch it on to enable full trust for it.  If you forget this step, all SSL connections will likely to fail.

   <img src="http://imgur.com/k62CUwH.png"/>

5. You can start recording network traffic:

   <img src="http://imgur.com/c0q6j2j.png"/>

## References

* <https://www.charlesproxy.com/documentation/configuration/browser-and-system-configuration/>
* <https://www.charlesproxy.com/documentation/using-charles/ssl-certificates/>
