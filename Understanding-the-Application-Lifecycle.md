TODO:
* Awaking from Push Notifications
* Awaking from Location Changes

# State Changes in an iOS app
An iOS application can transition between the following states:

1. Not running
2. Inactive (e.g. there is a phone call)
3. Active
4. Background
5. Suspended

![State Changes in an iOS App](http://i.imgur.com/aeOPsHh.png)

# The application delegate

Most of the time, your app reacts to state changes through the application delegate. If you are not using storyboards you will need to set up the window and root view controller of your application in `application:didFinishLaunchingWithOptions:`

For example:

```swift
    func application(application: UIApplication,
            didFinishLaunchingWithOptions launchOptions:[NSObject: AnyObject]?) -> Bool {
        
        self.window = UIWindow(frame: UIScreen.mainScreen().bounds)
        self.rootViewController = ViewController()
        self.window?.rootViewController = self.rootViewController
        self.window?.makeKeyAndVisible()
        
        return true
    }
```

If you are using a storyboard you simply specify specify the "main interface" in your project settings. (Remember to also set the initial view controller on the storyboard)