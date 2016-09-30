## Overview

While setting up breakpoints in your code is usually the first place to check, it often can be useful to see the network traffic exchanged between your iOS device and a server.  There are often things you want to check:

* Am I using the correct API key?
* Am I passing in the correct HTTP GET or POST parameters?
* Do I have the right URL pattern?
* What is the server error code?
* What is the server response?

It is often useful to monitor the network traffic to help diagnose these issues.  One way to monitor network traffic on your iOS device is to setup an HTTP proxy that can intercept the network requests.  The process requires two parts: one on the PC that will act as the proxy, and the other on the iOS device.

## Setting up a Proxy

***On your PC***:

1. Download a 30-day trial of [Charles Proxy](https://www.charlesproxy.com/download/).  There are versions available for Windows, Mac OS, and Linux.

2. You will need to insert a special root certificate in order to inspect SSL traffic.  If you are running an iOS simulator, run Charles Proxy and navigate to `Help` -> `SSL Proxying` -> `Install Charles Root Certificate on iOS Simulators`.

     <img src="http://imgur.com/Ac5QR0x.png"/>

3. SSL sites that must be inspected must be explicitly declared by going to `Proxy` -> `SSL Proxying Settings`.  Type the domain name and port 443 assuming the site is connecting to a standard SSL port.

     <img src="http://imgur.com/YXTqq93.png"/>

4. Make sure to ascertain your IP address of the machine with the proxy installed.  You can go to the `Help` -> `Local IP Address` to find it:

<img src="http://imgur.com/AwbbEwA.png"/>

***On your phone:***

1. If you are on an iOS device, open Safari and go to [http://www.charlesproxy.com/getssl](http://www.charlesproxy.com/getssl).  If you are using the simulator, the certificate should already be installed if you followed the previous steps.

2. Go to the Settings app, tap Wi-Fi, find the network you are connected to and then tap it to configure the network. 

   <img src="http://imgur.com/8KUEzr1.png"/>

   Scroll down to the HTTP Proxy setting, tap Manual. 

   Enter the IP address of your computer running Charles in the Server field, and the port Charles is running on in the Port field (usually 8888). Leave Authentication set to Off.
     
3. Connect to any web site.  **You need to go back to the PC to grant authorization for the device to connect.**

Go back to your PC and start recording network traffic!

<img src="http://imgur.com/c0q6j2j.png"/>

## References

* <https://www.charlesproxy.com/documentation/configuration/browser-and-system-configuration/>
* <https://www.charlesproxy.com/documentation/using-charles/ssl-certificates/>
