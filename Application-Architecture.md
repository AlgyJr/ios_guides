An iOS project is composed of many files that define different aspects
the application.  This guide presents high level overview of the most
common pieces of an application and how they fit together.  A more
comprehensive guide by Apple can be found [here][appguide].

[appguide]: https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Introduction/Introduction.html

## Application entry points and the `UIApplicationDelegate`

All iOS applications are instances of [`UIApplication`][uiapplication].
Each application has a number events in its lifecycle (e.g. launched,
went into the background, terminated).  As with other classes in the
UIKit framework, you control how the application will respond to these
events by providing it with a [delegate][delegatepattern].  A
`UIApplication`'s delegate must implement the
[`UIApplicationDelegate`][uiapplicationdelegate] protocol.

When you create a new application in Xcode, the IDE automatically sets
up the code to instantiate a `UIApplication` when the project is run.
It also generates a class that implements `UIApplicationDelegate` called
`AppDelegate` and sets the application's [delegate
property][uiapplicationdelegateprop] to an instance of this class.

_NB: In Swift projects this is done via the `@UIApplicationMain`
[attribute].  In Object-C projects the set up is explicitly done in the
`main.m` file._

[delegatepattern]: http://en.wikipedia.org/wiki/Delegation_pattern
[uiapplicationdelegate]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html
[uiapplication]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplication_Class/
[uiapplicationdelegateprop]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplication_Class/#//apple_ref/occ/instp/UIApplication/delegate
[attribute]: https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Attributes.html

### The entry point to your application
Of the methods that you can implement in your `UIApplicationDelegate`,
perhaps the most important is
[didFinishLaunchingWithOptions][didfinishlaunching].  This method will
be called once iOS has done most of the system initialization for your
app.  It is commonly used as an entry point where you can implement
custom logic that initializes your application and sets up the first
view controller.  For example, in applications that require log ins,
this is a good place to check whether there is a current user and
initialize different view controllers depending the "logged in" state.

[didfinishlaunching]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intfm/UIApplicationDelegate/application:didFinishLaunchingWithOptions:

### The root view controller

The `UIApplicationDelegate` also provides a reference to the
application's main [window][mainwindow] object.  Of particular
importance is the ability to set the main window's _root view
controller_.  This is the first view controller that will load and
present its view to the user.  More on view controllers can be found
below.

[mainwindow]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intfp/UIApplicationDelegate/window

#### The root view controller in storyboard applications
In simple storyboard applications, you can avoid working with the
`UIApplicationDelegate` and simply set the root view controller directly
in your `Main.storyboard`.  By default Xcode will generate an initial
view controller for you and set it to be the root view controller.  This
is indicated by the arrow that points to the view controller.  Once you
start adding more view controllers to your application, you can change
the root view controller by dragging this this arrow around.  You can
also select a view controller and set it to be the root view controller
by using the `Is Initial View Controller` checkbox in the Attributes
Inspector.

<!--- TODO: image of arrow and checkbox for initial view controller -->

#### Programatically setting the root view controller
In applications not using storyboards, you must programatically set the
root view controller.  This is done in your `UIApplicationDelegate`
implementation's `didFinishLaunchingWithOptions` method.

```swift
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        if let window = self.window {
            let vc = UIViewController()

            // This can be any subclass of UIViewController.  You can also use conditional logic
            // here to set up different view controllers depending on application state
            window.rootViewController =  vc

            // by default a UIViewController's .view property is set to an instance of UIView
            // of course you can set this to any custom subclass of UIView
            // one way to do this is subclass UIViewController and override the loadView method
            vc.view.backgroundColor = UIColor.cyanColor()

            let label = UILabel()
            label.text = "hello, world!"
            label.frame = vc.view.frame

            vc.view.addSubview(label)
        }

        return true
    }
    ...
}
```
<!-- TODO: insert image here of above code-->


### Other hooks in the application lifecycle
The `UIApplicationDelegate` protocol provides many other hooks into the
application's lifecycle giving you opportunities to things like respond
differently depending on how your application was launched, set up and
tear down temporary state depending whether your app is in the
foreground, and persist data before exiting. A detailed discussion of
the application lifecycle can be found [here][applifecycle].

[applifecycle]: https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/TheAppLifeCycle/TheAppLifeCycle.html#//apple_ref/doc/uid/TP40007072-CH2-SW3

## Model-View-Controller in iOS
Most applications you write for iOS will use a model-view-controller
(MVC) architecture similar to the one described by Apple
[here][applemvc].  This is a proven design that helps to break up your
application into managable parts with clean separation of concerns.
What follows is a description of how each part of MVC typically
applies in iOS programming.

<!--- TODO: build sample app here -->

_NB: This description of MVC is actually slightly different than the one
originally described by [Krasner and Pope] in the 1980s, and is probably
closer to what some people would call a model-view-presenter paradigm.
An interesting discussion of the evolution of MVC architecture over time
can be found by Martin Fowler can be found [here][fowlermvc]._

[applemvc]: https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html
[smalltalkmvc]: https://www.lri.fr/~mbl/ENS/FONDIHM/2013/papers/Krasner-JOOP88.pdf
[fowlermvc]: http://martinfowler.com/eaaDev/uiArchs.html

### Models
The model layer handles the important logic of your app. In a photo-sharing social network, it would include entities like "Users" and "Photos." Often, the model handles storing data.

The model should be presentation agnostic. A well designed model could be reused between iPhone, iPad, and Mac.

- api
- serialization
- representation of data

### Views


- nib
- storyboard
- classes


- translate low level events into semantic ones

Everything involving UI and presentation falls into the view. UIKit provides the base classes, such as `UIView`, `UIButton`, and `UIImageView`. You can subclass and create your own views, but the views provided by UIKit are highly customizable, and you should stick to them whenever possible.

### View Controllers


The controller sits between your model and view. When you tap a button, the view controller should handle the event and update the model. If the model updates, the view controller reflects this by updating the view.

Each view controller usually correlates to one screen within your app. For instance, an email client would probably have an `AccountsListViewController`. If you clicked on an account, it would push in to an `InboxViewController`, which displays a list of emails. If you tapped an email, it would display the full email within an `EmailDetailsViewController`.

Your view controllers will always subclass from `UIViewController`, provided by UIKit. You can handle common events by overriding methods. For instance, if you want to start polling an endpoint when your view appears on screen, you could override `viewDidAppear(animated: Bool)`

## Other
### Build settings
### Storyboards
### Assets catalogs
