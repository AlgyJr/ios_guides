## Overview

### Upgrading to Xcode 8

Currently, iOS 10 devices will not run against Xcode 7.3.1 (you will see `Could Not Find Developer Disk Image`).  If you have yet to update but have concerns about Swift 2.3 or Swift 3.0 version compatibility (especially with third-party libraries) with your existing code base, you should keep your Xcode 7 version by renaming the directory.  Here are the basic steps:

1. Rename your Xcode.app to Xcode7.app:

      ```bash
      sudo mv /Applications/Xcode.app /Applications/Xcode7.app
      ```

2. Download Xcode 8 from the app store.  You need to upgrade your OS version to at least 10.11.5.  There are two updates, which will likely take about 2 hours to download.
3. Run Xcode 8 once to install it.
4. Open the terminal and create a symbolic link from Xcode 8 Developer Disk Image 10.0 to Xcode 8 Developer 
Disk Image folder using this command:

       ```bash 
       sudo ln -s /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport/10.0 (14A345)/ /Applications/Xcode7.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport/10.0
       ```

   Xcode 8 has a backwards compatible Swift 2.3 version, but there are still some minor changes (i.e. first argument parameters labels) from the Swift 2.3 version supported in Xcode 7.3.1.  There is a built-in converter tool can usually help handle most of these minor fixes, but if there are third-party library dependencies, you may need to first check what version it supports.  

5. Restart Xcode so that it finds the new image.

### Downgrading to Xcode 7.3.1

If you accidentally upgraded to Xcode 8 and wish to get to Xcode 7 that can run in parallel, you can follow similar steps:

1. Rename your Xcode 8 version:
   ```bash
   sudo mv /Applications/Xcode.app /Applications/Xcode8.app
   ```
2. Download Xcode 7.3.1 by first signing into [https://developer.apple.com/download/more/](https://developer.apple.com/download/more/) first.  You can then download [Xcode 7.3.1](http://adcdownload.apple.com/Developer_Tools/Xcode_7.3.1/Xcode_7.3.1.dmg) from the Apple Developer store. 

3. Reinstall the Xcode 7.3.1 image.

## References

* <http://stackoverflow.com/questions/39576363/could-not-find-developer-disk-image-ios-10>
* <http://stackoverflow.com/questions/37945376/use-xcode-7-with-ios-10>