To a user, a local notification looks the same as a push notification. However, the notifications are generated from the app, not a server. They are typically used for scheduled events, like an alarm clock or a to do item reminder.

## Step 1: Request Permissions

Add the code below to request permission to send the user notifications. For testing, you can add it in the AppDelegate.swift in the `application:didFinishLaunchingWithOptions:` function. This will cause the permissions alert to display as soon as the user launches the app. Be careful though: if a user denies the permission, the app will never show the alert again even if you request the permission again. During testing, if you accidentally denied the permission, you can delete the app from the simulator to reset it.

```swift
UIApplication.sharedApplication().registerUserNotificationSettings(UIUserNotificationSettings(forTypes: [.Alert, .Badge, .Sound], categories: nil))
```

The code above will display an alert like this:

![Permission alert](http://i.imgur.com/cxrvSn1.png)

## Step 2: Schedule a Notification

Create a UILocalNotification object that configures your notification. A key parameter is the fireDate property which determines when the alert will be shown. Configure the fireDate by creating an NSDate object that has the date/time you want the notification to be shown.

```swift
// Create a date 10 seconds from the current time
let fireDate = NSDate(timeIntervalSinceNow: 10)

let notification = UILocalNotification()
notification.alertBody = "This is a notification!"

// text that is displayed after "slide to..." on the lock screen - defaults to "slide to view"
notification.alertAction = "open"

notification.fireDate = fireDate
notification.soundName = UILocalNotificationDefaultSoundName

UIApplication.sharedApplication().scheduleLocalNotification(notification)
```

### Step 3: Close the App

Notifications are not shown if the application is active. After scheduling the notification, in the simulator, press Command-Shift-H to return to the Home screen.