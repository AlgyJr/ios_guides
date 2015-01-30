Most iOS apps follow the same basic structure and patterns.

# Model-View-Controller

Most of your code can be broken into three categories: model, view, controller.

## Model

The model layer handles the important logic of your app. In a photo-sharing social network, it would include entities like "Users" and "Photos." Often, the model handles storing data.

The model should be presentation agnostic. A well designed model could be reused between iPhone, iPad, and Mac.

## View

Everything involving UI and presentation falls into the view. UIKit provides the base classes, such as `UIView`, `UIButton`, and `UIImageView`. You can subclass and create your own views, but the views provided by UIKit are highly customizable, and you should stick to them whenever possible.

## Controller

The controller sits between your model and view. When you tap a button, the view controller should handle the event and update the model. If the model updates, the view controller reflects this by updating the view.

Each view controller usually correlates to one screen within your app. For instance, an email client would probably have an `AccountsListViewController`. If you clicked on an account, it would push in to an `InboxViewController`, which displays a list of emails. If you tapped an email, it would display the full email within an `EmailDetailsViewController`.

Your view controllers will always subclass from `UIViewController`, provided by UIKit. You can handle common events by overriding methods. For instance, if you want to start polling an endpoint when your view appears on screen, you could override `viewDidAppear(animated: Bool)`

# The App Delegate

If you want to hook into important events in the application lifecycle, you implement delegate methods in your app delegate. Events could include application launch, entering the background, and receiving incoming push notifications.

For instance, if you want to execute code after the application launches, you override:

```
func application(application: UIApplication!, didFinishLaunchingWithOptions launchOptions: NSDictionary!) -> Bool {
  // Your code here
}
```

# Services and Extensions

Your app may provide services to the OS, and other apps. For instance, you can register a custom URL scheme, so when someone launches, `myApp://home`, it opens your app, and you present the user with their home screen. You can add this support by adding a few keys to your `Info.plist`.

With iOS 8, there are a number of new extensions available. You can add content to Notification Center, provide custom keyboards, or add a new sharing option. These extensions are run independent of your main app, but they are bundled within your main app.

More: [Understand How an App Extension Works](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionOverview.html)

# System Frameworks

The more interesting functionality of the device is exposed to you via system frameworks. For instance, if you want to get the user's current location, you only need to import the *Core Location* framework, and write a few lines of code.

You spend most of your time working with UIKit, which provides all the view classes. You'll often work with Foundation, which provides the basic building blocks of the language, such as strings, numbers, arrays, dictionaries.

Other interesting frameworks include:

* Address Book - Accessing the user's contacts
* Map Kit - Displaying maps
* EventKit - Working with the user's calendar
* Core Graphics - Drawing
* AVFoundation - Displaying audio and video
* Core Motion - Accesing accelerometer and gyroscope data

The higher level frameworks are usually exposed in Objective-C or Swift, higher level languages. Lower level APIs, such as Core Graphics, follow a C API.

* [iOS Technologies Overview](https://developer.apple.com/library/ios/documentation/Miscellaneous/Conceptual/iPhoneOSTechOverview/Introduction/Introduction.html)
* [iOS Frameworks](https://developer.apple.com/library/ios/documentation/miscellaneous/conceptual/iphoneostechoverview/iPhoneOSFrameworks/iPhoneOSFrameworks.html)
* [What are Frameworks?](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html)

# External Frameworks

If you're coming from web or backend development, you'll find yourself using fewer external libraries than you're used to. You'll find yourself getting surprisingly far with only the frameworks Apple provides. However, you will likely integrate an external library here and there to fill in gaps. It may be an open source library, or an internal library used by your company.


The Apple-recommended solution is to use a dynamic framework. Dynamic frameworks behave similar to system frameworks, but you can bundle them alongside your app.

A more extensive solution is the [CocoaPods](http://cocoapods.org) open source project. This tool acts similar to Ruby's *Bundler*, allowing you to specify external dependencies in a `.podspec` file, and run a single command to load them into your project.

More: [Framing Your Work](http://subjectiveobserver.wordpress.com/2014/06/) - Bundling your work in a dynamic framework

# References

* [Start Developing iOS Apps Today](https://developer.apple.com/library/ios/referencelibrary/GettingStarted/RoadMapiOS/index.html#//apple_ref/doc/uid/TP40011343)