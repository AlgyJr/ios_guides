## The Objective-C Bridging Header
If you want to consume Objective-C code from Swift, you'll need to use a [bridging header] (https://developer.apple.com/library/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html).

If you didn't create one when you first created the project:

This can be done by `File -> New -> File ... -> iOS -> Source -> Header
File`.  You'll want to name this file something like
`YourProject-Bridging-Header.h`, but the name doesn't actually affect
the build process.  You'll need to specify this file as a bridging
header in your project's `Build Settings`.

<a href="http://imgur.com/aP32OZC"><img src="http://i.imgur.com/aP32OZC.png" title="Objective C Bridging" /></a>

You will then `#import` every Objective-C header you want to expose to
Swift in this file.
