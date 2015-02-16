<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Overview](#overview)
- [Storyboard setup](#storyboard-setup)
  - [Step 1: Add tab bar controller as initial view controller](#step-1-add-tab-bar-controller-as-initial-view-controller)
  - [Step 2: Add a view controller for each tab](#step-2-add-a-view-controller-for-each-tab)
  - [Step 3: Customize the bar item for each tab](#step-3-customize-the-bar-item-for-each-tab)
  - [Step 4: Create view controller classes for tabs](#step-4-create-view-controller-classes-for-tabs)
  - [Step 5: Set custom class for and finish design of tabs](#step-5-set-custom-class-for-and-finish-design-of-tabs)
- [Programmatic setup](#programmatic-setup)
  - [Step 1: Create tab bar controller and as initial view controller](#step-1-create-tab-bar-controller-and-as-initial-view-controller)
  - [Step 2: Create a view controller for each tab](#step-2-create-a-view-controller-for-each-tab)
  - [Step 3: Customize the bar item for each view controller](#step-3-customize-the-bar-item-for-each-view-controller)
  - [Step 4: Place view controllers inside the tab bar controller](#step-4-place-view-controllers-inside-the-tab-bar-controller)
- [Further reading](#further-reading)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Overview
<a href="http://imgur.com/WhoidE5"><img src="http://i.imgur.com/WhoidE5.gif" title="source: imgur.com" /></a>

Tab bars controllers provide a simple interface for a users to switch
between a set of view controllers.  This quickstart covers the basic
procedure for setting up a tab bar controller and setting up the view
controllers for each tab.

## Storyboard setup
### Step 1: Add tab bar controller as initial view controller
In Interface Builder drag a `Tab Bar Controller` from the Object Library
into your storyboard.  It will come preconfigured with two tabs
corresponding to two view controllers.  Many times you will want your
tab bar controller to be the initial view controller.  You can set this
by selecting the tab bar controller and ticking `Is Initial View
Controller`.

<a href="http://imgur.com/WgiEuBZ"><img src="http://i.imgur.com/WgiEuBZ.png" title="source: imgur.com" /></a>

### Step 2: Add a view controller for each tab
The tab bar controller is configured with two tabs by default.  You can
delete a tab by selecting the corresponding view controller and deleting
it the storyboard.  To add a tab, first drag a new `View Controller`
object to the storybard.  Next control-drag from the tab bar controller
to new view controller and select `view controllers` under `Relationship
Segue`.  Your tab bar controller will update with a new tab.

<a href="http://imgur.com/OOMB573"><img src="http://i.imgur.com/OOMB573.gif" title="source: imgur.com" /></a>

### Step 3: Customize the bar item for each tab
You can customize appearance and title of each button on the tab bar by
selecting the tab bar item **inside the corresponding view controller**.
In particular, you'll likely want to an image for each tab bar item.

<a href="http://imgur.com/nZ2g4fp"><img src="http://i.imgur.com/nZ2g4fp.png" title="source: imgur.com" /></a>

### Step 4: Create view controller classes for tabs
You'll need to create classes to contain the code for the view
controllers corresponding to each tab.  Select `File -> New -> iOS ->
Source -> Cocoa Touch Class` and create a new subclass of
`UIViewController` for each kind tab you will have.

### Step 5: Set custom class for and finish design of tabs
For each tab in your storyboard select the corresponding view controller
and set its custom class to one of the classes you just created.  You
can now add other components to this tab and connect outlets to your
view controller class as you would with any other view controller.

<a href="http://imgur.com/5YWL3Vw"><img src="http://i.imgur.com/5YWL3Vw.png" title="source: imgur.com" /></a>

## Programmatic setup
### Step 1: Create tab bar controller and as initial view controller
You can instantiate a tab bar controller programmatically and use it as
you would any othe view controller.  If you need it to be your root view
controller the best place to do this is in the app delegate.

```swift
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        let tabBarController = UITabBarController()
        window = UIWindow(frame: UIScreen.mainScreen().bounds)
        window?.rootViewController = tabBarController
        window?.makeKeyAndVisible()

        ...

        return true
    }
    ...
}
```

### Step 2: Create a view controller for each tab
You initialize your other view controllers as you would normally.

```swift
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        ...
        let vc1 = UIViewController()
        let vc2 = UIViewController()
        vc1.view.backgroundColor = UIColor.orangeColor()
        vc2.view.backgroundColor = UIColor.purpleColor()
        ...
    }
    ...
}
```

### Step 3: Customize the bar item for each view controller
You customise the appearance of the tab bar item for each tab by
manipulating the `tabBarItem` property _on the corresponding view
controller_.

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        ...
        vc1.tabBarItem.title = "Orange"
        vc1.tabBarItem.image = UIImage(named: "heart")
        vc2.tabBarItem.title = "Purple"
        vc2.tabBarItem.image = UIImage(named: "star")
        ...
    }
    ...
}
```

### Step 4: Place view controllers inside the tab bar controller
Finally you can set your view controllers as tabs inside the tab bar
controller.  Do this by setting the `viewControllers` array on the the
tab bar controller.  Putting everything together we have

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {

        let tabBarController = UITabBarController()
        window = UIWindow(frame: UIScreen.mainScreen().bounds)
        window?.rootViewController = tabBarController
        window?.makeKeyAndVisible()
        let vc1 = UIViewController()
        let vc2 = UIViewController()
        vc1.view.backgroundColor = UIColor.orangeColor()
        vc2.view.backgroundColor = UIColor.purpleColor()

        vc1.tabBarItem.title = "Orange"
        vc1.tabBarItem.image = UIImage(named: "heart")
        vc2.tabBarItem.title = "Purple"
        vc2.tabBarItem.image = UIImage(named: "star")

        tabBarController.viewControllers = [vc1, vc2]

        return true
    }
    ...
}
```

<a href="http://imgur.com/GhzQOTw"><img src="http://i.imgur.com/GhzQOTw.gif" title="source: imgur.com" /></a>

## Further reading
* [Official Tab Bar Guide from Apple][appleguide]

[appleguide]: https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/TabBarControllers.html
