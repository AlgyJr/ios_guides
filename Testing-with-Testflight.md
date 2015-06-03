[Testflight](https://developer.apple.com/testflight/) allows you to invite users to beta test your apps before they are available on the App store. This guide will walk you through the details of submitting an app that can be tested and invite users to test your app.

### Step 1: Create an App Record in iTunesConnect

Open [iTunesConnect](https://itunesconnect.apple.com) and log in with your AppleID. Select the MyApps icon to go to manage your App Records. Click the + icon and select "New iOS App" as shown here.

![Create an App in iTunes Connect|700](http://i.imgur.com/95JJRJU.gif)

Note that the SKU simply needs to be a number that is greater than 1 digit and is not used by another App.

### Step 2: Find your Bundle ID and Version number
Also,  your Bundle ID + Bundle Suffix must be unique and match the bundle ID in XCode and your version number must also match the one in XCode. You can check the bundle ID and version of your App by clicking on your project in the far left Project Navigator window as shown here:

![Check bundle ID and version|700](http://i.imgur.com/bTMLewo.gif)

### Step 3: Set an Icon
You cannot submit your App for testing without at least one icon image for your App. Add an icon for the default as shown here:

![Add Icon|700](http://i.imgur.com/nJkyncD.gif)

You can use a site like [Makeappicon](http://makeappicon.com/) to construct images of all the useful dimensions for you.

### Step 4: Archive and Submit
We need to archive the app, validate it, and then submit it so that a build of it will show up in iTunesConnect. First, switch the build target to iOS Device. Then, select the Product | Archive menu option, which will bring up the Archive Organizer. Click on the "Validate..." button, which will start a process that may run for up to 15 minutes. (Sometimes this hangs for some reason so if it runs longer than 15 minutes you may want to cancel it and run again). If you did not have any validation errors you will now be able to submit your App to iTunesConnect. Click on the "Submit..." button and wait again. (If you had any errors in Validation you will not be able to submit and must fix those and then perform Validation again)

![Archive and Submit|700](http://i.imgur.com/LAce2LX.gif)

### Step 5: Add User Roles

If you need to add new users to your project go to the Users and Roles section of [iTunesConnect](https://itunesconnect.apple.com). And follow the wizard. Note that the email needs to be the user's AppleID.

![Users and Roles|700](http://i.imgur.com/37E2TfW.gif)

### Step 6: Add Internal Testers

Go to the MyApps section of [iTunesConnect](https://itunesconnect.apple.com) and select your App. Then, go to the prerelease subsection of your App. And check any users that you would like to test your App. Then click the Save button when you are done.
![Add Internal Testers|700](http://i.imgur.com/KJ0Vsa6.gif)


### Step 7: Turn Testing On

Go back to the Builds section of Prerelease and flip the TestFlight Beta Testing switch on. If you go back to the Internal Testers section you will notice that the users you selected for testing have now been invited.
![Turn Testing On|700](http://i.imgur.com/pNs3JOG.gif)