## Overview

Traditionally in iOS apps a
[view controller](#Application-Architecture#view-controllers)
corresponds to one "screen" in the application.
Apple provides a few [container view controllers][containercatalog]&mdash;such
as the [navigation controller](Navigation-Controller#) and [tab bar
controller](Tab-Bar-Controller-Guide#)&mdash;to help developers manage
and transition between multiple view controllers.

[containercatalog]: https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Introduction.html

To build really unique user experiences, you can also define your own
custom container view controllers that manage the display of and
navigation between _child view controllers_.  This guide provides a
quick start recipe for doing this by building a simple container view
controller `TwoTabViewController` that allows the user to switch between
two view controllers.

<a href="http://imgur.com/CbM1kVR"><img src="http://i.imgur.com/CbM1kVR.gif" title="source: imgur.com" /></a>

## Step 1: Create subclass of UIViewController
Create a new subclass of `UIViewController` by selecting `File -> New ->
File...  -> Source -> Cocoa Touch Class`.  Tick the checkbox named `Also
create XIB file`.

## Step 2: Design container view controller in Interface Builder
You'll generally want a way for the user to control or navigate between
the child view controllers.  You'll probably also want add one or more
blank view elements to serve as placeholders for the views of the child
view controllers.  Add `@IBOutlets` for any components you'll need
programatically manipulate and  `@IBActions` for any events to which you
want to respond.

<a href="http://imgur.com/vEx95BQ"><img src="http://i.imgur.com/vEx95BQ.gif" title="source: imgur.com" /></a>

## Step 3: Add code to keep track of child view controllers
You'll need a way to track of all the view controllers you are
"containing".  For example the navigation controller maintains a stack
of view controllers.  In our two tab example, we keep a separate
property to reference each of the child view controllers.

```swift
class TwoTabViewController: UIViewController {
    ...
    var firstViewController: UIViewController?
    var secondViewController: UIViewController?
    ...
}

```

## Step 4: Add logic to add and remove child views
The main responsibility of a container view controller is to display the
child view controllers' views.  You'll need to add code to keep track of
which child views are currently being shown.  You'll also need help iOS
propagate events (such as `viewDidLoad`) to the child view controllers
by calling certain methods before and after adding/removing a child view
controller's view.

In our two tab example we keep a variable `activeViewController`.
Whenever `activeViewController` is set we remove the old one's view and
add the new one's view as a subview of our content view.

```swift
class TwoTabViewController: UIViewController {
    ...

    private var activeViewController: UIViewController? {
        didSet {
            removeInactiveViewController(oldValue)
            updateActiveViewController()
        }
    }

    private func removeInactiveViewController(inactiveViewController: UIViewController?) {
        if let inActiveVC = inactiveViewController {
            // call before removing child view controller's view from hierarchy
            inActiveVC.willMoveToParentViewController(nil)

            inActiveVC.view.removeFromSuperview()

            // call after removing child view controller's view from hierarchy
            inActiveVC.removeFromParentViewController()
        }
    }

    private func updateActiveViewController() {
        if let activeVC = activeViewController {
            // call before adding child view controller's view as subview
            addChildViewController(activeVC)

            activeVC.view.frame = contentView.bounds
            contentView.addSubview(activeVC.view)

            // call before adding child view controller's view as subview
            activeVC.didMoveToParentViewController(self)
        }
    }
    ...
}
```

## Step 5: Handle events
You'll often have update which child view controllers' views are being
displayed in response to system or user events.  Here we set the active
view controller to the be first one when our container view controller
loads.  We also update the active view controller in response to user
taps.


```swift
class TwoTabViewController: UIViewController {
    ...
    override func viewDidLoad() {
        super.viewDidLoad()
        activeViewController = firstViewController
    }

    @IBAction func didTapFirstButton(sender: AnyObject) {
        activeViewController = firstViewController
    }

    @IBAction func didTapSecondButton(sender: AnyObject) {
        activeViewController = secondViewController
    }
}
```

## Step 6: Use custom container in your application
As of this writing, there is no easy way to use and configure custom
container view controllers in a storyboard.  Your best bet is to
instantiate and configure any custom container view controllers
programmatically.  If your custom container is
the root view controller, a good place to do this is in the
[app delegate](Application-Architecture#the-entry-point-to-your-application)

```swift
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        let vc1 = UIViewController()
        let vc2 = UIViewController()

        vc1.view.backgroundColor = UIColor.orangeColor()
        vc2.view.backgroundColor = UIColor.purpleColor()

        let twoTabVC = TwoTabViewController(nibName: "TwoTabViewController", bundle: nil)
        twoTabVC.firstViewController = vc1
        twoTabVC.secondViewController = vc2

        window = UIWindow(frame: UIScreen.mainScreen().bounds)
        window?.rootViewController = twoTabVC
        window?.makeKeyAndVisible()

        return true
    }
    ...
}

```

## Further reading
* [Our container view controllers guide](Container-View-Controllers) gives a more in depth
  discussion
* [Apple's official guide][appleguide] provides comprehensive coverage
  of this topic

[appleguide]: https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/ImplementingaContainerViewController.html