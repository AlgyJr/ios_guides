### Option 1:
In the **info.plist** file, you can use `NSAllowsArbitraryLoads` to completely disable ATS in your app:

![Imgur](http://i.imgur.com/Tmy1wYL.png)

### Option 2:
Right-Click info.plist and **Open As -> Source Code**. This will open the file in XML format.
- Add the following to the bottom of the info.plist xml file.
```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key><true/>
</dict>
```

**This is strongly discouraged.** Only use this during development.

### Read More

[Working with Apple's App Transport Security](http://www.neglectedpotential.com/2015/06/working-with-apples-application-transport-security/)