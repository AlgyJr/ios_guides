[Testflight](https://developer.apple.com/testflight/) allows you to invite users to beta test your apps before they are available on the App store. This guide will walk you through the details of submitting an app that can be tested and invite users to test your app.

### Step 1: Create an App Record in iTunesConnect

Open [iTunesConnect](https://itunesconnect.apple.com) and log in with your AppleID. Select the MyApps icon to go to manage your App Records. Click the + icon and select "New iOS App" as shown here.

![Create an App in iTunes Connect|700](http://i.imgur.com/95JJRJU.gif)

Note that the SKU simply needs to be a number that is greater than 1 digit and is not used by another App.

### Step 2: Find your Bundle ID and Version number
Also,  your Bundle ID + Bundle Suffix must be unique and match the bundle ID in XCode and your version number must also match the one in XCode. You can check the bundle ID and version of your App by clicking on your project in the far left Project Navigator window as shown here:

![Check bundle ID and version|700](http://i.imgur.com/bTMLewo.gif)