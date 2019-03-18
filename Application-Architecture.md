An iOS project is composed of many files that define different aspects
of the application.  This guide presents high level overview of the most
common components of an application and how they fit together.  A more
comprehensive guide by Apple can be found [here][appguide].

[appguide]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Introduction/Introduction.html

## Structure of a `UIApplication`

All iOS applications are instances of [`UIApplication`][uiapplication].
Each application has a number of events in its lifecycle (e.g. launched,
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
initialize different view controllers depending on the "logged in" state.

[didfinishlaunching]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intfm/UIApplicationDelegate/application:didFinishLaunchingWithOptions:

### The root view controller

The `UIApplicationDelegate` also provides a reference to the
application's main [window][mainwindow] object.  Of particular
importance is the ability to set the main window's _root view
controller_.  This is the first view controller that will load and
present its view to the user.  More on root view controllers can be
found below.

[mainwindow]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intfp/UIApplicationDelegate/window

#### The root view controller in storyboard applications
In simple storyboard applications, you can avoid working with the
`UIApplicationDelegate` and simply set the root view controller directly
in your `Main.storyboard`.  By default Xcode will generate an initial
view controller for you and set it to be the root view controller.  This
is indicated by the arrow that points to the view controller.  Once you
start adding more view controllers to your application, you can change
the root view controller by dragging this arrow around.  You can
also select a view controller and set it to be the root view controller
by using the `Is Initial View Controller` checkbox in the Attributes
Inspector.

<a href="https://imgur.com/winIGnh"><img src="https://i.imgur.com/winIGnhl.png" title="Setting Root View Controller" /></a>

#### Programmatically setting the root view controller
In applications not using storyboards, you must programmatically set the
root view controller.  This is done in your `UIApplicationDelegate`
implementation's `didFinishLaunchingWithOptions` method.

```swift
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        let vc = UIViewController()

        let label = UILabel()
        label.text = "hello, world!"
        label.frame = vc.view.frame

        // by default a UIViewController's .view property is set to an instance of UIView
        // of course you can set this to any custom subclass of UIView
        // one way to do this is subclass UIViewController and override the loadView method
        vc.view.backgroundColor = UIColor.cyan
        vc.view.addSubview(label)

        window = UIWindow(frame: UIScreen.main.bounds)

        // This can be set to any subclass of UIViewController.  You can also use conditional
        // logic here to set up different view controllers depending on application state
        window?.rootViewController = vc
        window?.makeKeyAndVisible()

        return true
    }
    ...
}
```

<a href="https://imgur.com/fXE9BWw"><img src="https://i.imgur.com/fXE9BWwl.png" title="Setting Root View Controller Programatically" /></a>

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
application into manageable parts with clean separation of concerns.
What follows is a description of how each part of MVC typically
applies in iOS programming.

<!--- TODO: build sample app here -->

_NB: This description of MVC is actually slightly different than the one
originally described by [Krasner and Pope][smalltalkmvc] in the 1980s,
and is probably closer to what some people would call a
model-view-presenter pattern.  An interesting discussion of the
evolution of MVC architecture over time by Martin Fowler can be found
[here][fowlermvc]._

[applemvc]: https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html
[smalltalkmvc]: https://www.lri.fr/~mbl/ENS/FONDIHM/2013/papers/Krasner-JOOP88.pdf
[fowlermvc]: http://martinfowler.com/eaaDev/uiArchs.html

### Models
Model objects encapsulate a logical unit of domain specific data in your
application.  In a photo-sharing social network, they might include
entities like "Users" and "Photos".  Models may have relationships with
other models.  For example, a "Photo" might have been "posted by" a
"User".  The model layer will generally contain any domain specific
logic for manipulating your data and relationships.  For example if a
User has a field keeping track of the number of Photos she has posted,
this field will have to updated once she posts a new Photo.

Often in an iOS application, much of the core logic around manipulating
models and their relationships is offloaded to a remote API (e.g. a REST
API).  In these cases, the models in your iOS project will generally be
some Swift (or Objective-C) representation of the resources defined by
the API.

The model layer is responsible for knowing how to perform [CRUD][crud]
operations on models.  This can include having Swift (or Objective-C)
bindings to right API endpoints and implementing logic to
serialize/deserialize models from/into the different data formats.
[Mantle](https://github.com/Mantle/Mantle) and
[RestKit](https://github.com/RestKit/RestKit) are two popular libraries
that help to define the model layer&mdash;specially the
serialization/deserialization logic when working with REST APIs.

[crud]: http://en.wikipedia.org/wiki/Create,_read,_update_and_delete

In the model-view-controller pattern the model is responsible for
notifying related controllers that there has been a change in its state.
This happens for example when the model layer has finished loading
data from a remote service and informs the view controller so that it
can transition out of the loading state and notify the views to display
the data.


### Views
Views are responsible for displaying data (literally [drawing
themselves][drawrect] on the screen) and responding to user actions.  Every
view in an iOS application will be a subclass of
[`UIView`][uiview]

[uiview]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html
[drawrect]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instm/UIView/drawRect:

UIKit comes with a selection of [predefined views][viewcatalog] that are
fairly comprehensive and, used creatively, can implement many UI
designs.

[viewcatalog]: https://developer.apple.com/library/content/navigation/#section=Topics&topic=User%20Experience

You can also create custom views by subclassing any `UIView` class.
Custom views are generally composed of one or more built-in views.  If
this is the case, the layout for a custom view can be defined using
Interface Builder in a [storyboard] or [nib], or done programatically in
the view's class.

[nib]: https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/NibFile.html
[storyboard]: https://developer.apple.com/library/ios/documentation/General/Conceptual/Devpedia-CocoaApp/Storyboard.html

You'll find yourself working with two kinds of views: reusable
components that are fairly generic (e.g. `UITableView`) and specific
views that meant for presenting a specific model (e.g. a [custom cell
class](Table-View-Guide#creating-customized-cells) in a `UITableView`).
In the former case you'll want to design a good interface so that your
view can be reused in many situations.  In the later case, it is OK for
your view to be tightly coupled to your model since its only purpose is
to present a visual representation of the model.

A view may be responsible for managing the instantiation and layout of
its subviews. However as you traverse up the [view
hieararchy][viewhierarchy] ultimately you'll find some parent view that
was instantiated by and inserted into the view hierarchy by a view
controller.  View controllers are responsible for creating views and
notifying views when they need to refresh themselves with the latest
data from the model.

[viewhierarchy]: https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/CreatingViews/CreatingViews.html#//apple_ref/doc/uid/TP40009503-CH5-SW47

Conversely, views are responsible for responding to user actions and
propagating events to view controllers so that they can handle them
appropriately, for example by updating the model.

Often views will translate a low level event into a semantic event.  For
example a table cell view responsible for displaying a Tweet might
translate the low level of the event button tap on the "star button"
into the semantic event of "user favorited Tweet".  The view would then
pass this high level message to the view controller who might then have
the model layer make an API call to complete the "favoriting".

The [delegate pattern] is useful for this kind of event propagation and
is used widely throughout iOS frameworks.

[delegate pattern]: https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html

### View Controllers

The controller sits between your model and view.  It coordinates the
interaction between the model and the view by passing on _state_ changes
from model to the view and propagating _events_ from the view to the
model.  This relationship can be summarized as follows:

```
                                             update view
     -- state change -->                 -- with new state -->
Model                     View Controller                     View
     <-- handle event --                 <-- interpret and ---
      by updating model                     propagate event

```

Your view controllers will always subclass from `UIViewController`.
Each view controller usually manages one "screen" within your app.  For
instance, an email client might have an `AccountsListViewController`. If
you clicked on an account, it would push in to an `InboxViewController`,
which displays a list of emails. If you tapped an email, it would
display the full email within an `EmailDetailsViewController`.

There is a notion of [container view controllers](Container-View-Controllers#) that
manage the multiple view controllers and their interactions.  The most
common built-in container view controller is the [[navigation controller|Navigation Controller]].

<!---
## Other
### Build settings
### Storyboards
### Assets catalogs
-->
