## Overview

<a href="http://imgur.com/WhoidE5"><img src="http://i.imgur.com/WhoidE5.gif" title="source: imgur.com" /></a>

Tab bar controllers are implemented by the
[UITabBarController][uitabbarcontroller] class.  They allow a user of to
switch between multiple arbitrary view controllers by maintaining an
[array of `UIViewControllers`][viewcontrollersprop].

They can be used to allow the user to navigate between entirely
different parts of your application, or they can be used to display two
different views of the same backing data.  Tab bar controllers also have
the built-in ability to display a "More..." interface when more than 5
tabs are added.  They can also let the user customize which tabs are
shown by on the main tab bar when there are more than 5 tabs.  An
example of this behavior is found in the standard iOS "Music" app
(previously "iTunes").

This guide covers common use cases for tab bar controllers.  A more in
depth guide by Apple can be found [here][tabbarcontrollers]

[tabbarcontrollers]: https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/TabBarControllers.html#//apple_ref/doc/uid/TP40011313-CH3-SW8
[viewcontrollersprop]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITabBarController_Class/#//apple_ref/occ/instp/UITabBarController/viewControllers
[uitabbarcontroller]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITabBarController_Class/

In order to demonstrate basic functionality of the `UITabBarController`
we will build a very basic clone of the standard iOS "Clock" app.  Our
app will have only two tabs one to display the current time, and the
other will implement the stopwatch functionality.

## Using tab bar controllers in storyboards
We create a new "Single View Application" and add two subclasses of
`UIViewController` via `File -> New -> iOS -> Source -> Cocoa Touch
Class`.  We create a `ClockViewController` and a
`StopwatchViewController`.

We open up our `Main.storyboard` and go ahead and delete the
pregenerated view controller.  Dragging a "Tab Bar Controller" from the
Object Library into the storyboard automatically creates two view
controllers that are already in the tab bar controller's array of view
controllers.  We also set the the the tab bar controller to be our
application's root view controller selecting it and by ticking the `Is
Initial View Controller` checkbox.

<a href="http://imgur.com/WgiEuBZ"><img src="http://i.imgur.com/WgiEuBZ.png" title="source: imgur.com" /></a>

### Adding tabs to the tab bar controller
We can add more tabs by dragging a new view controller frm
the Object Library onto the storyboard and then control-dragging from
the tab bar controller to our the new view controller and then selecting
`Relationship Segues -> view controllers`.  This will add the new view
controller to the tab bar controller's array of view controllers. It
also automatically adds a tab bar item to our new view controller which
will alow us to configure the appearance of the button that represents
this view controller in the tab bar.

<a href="http://imgur.com/OOMB573"><img src="http://i.imgur.com/OOMB573.gif" title="source: imgur.com" /></a>

In our case we only needed two view controllers total, but we removed
and readded one above to illustrate the technique.

We set the `Custom Class` property of one of the view controllers in our
tab bar to `ClockViewController` and the other to
`StopwatchViewController`.  Now we can design our view controllers and
connect `@IBOutlets` as we would any other view controller.

We add a single label to the `ClockViewController`.  It will display the
current time.  We add a label to the `StopwatchViewController` top
display the elapsed time and add two buttons for "start" and
"stop/reset".  We add `@IBOutlets` for our two labels and add
`@IBActions` to respond to each button being tapped.

<a href="http://imgur.com/Vc2uQK7"><img src="http://i.imgur.com/Vc2uQK7.png" title="source: imgur.com" /></a>

Finally we can add the code to make our clock tick

```swift
class ClockViewController: UIViewController {

    @IBOutlet weak var timeLabel: UILabel!

    var timer: NSTimer?
    let dateFormatter = NSDateFormatter()

    func updateTime() {
        timeLabel.text = dateFormatter.stringFromDate(NSDate())
    }

    override func viewDidLoad() {
        dateFormatter.dateFormat = "hh:mm:ss"
        updateTime()
    }

    override func viewDidAppear(animated: Bool) {
        super.viewDidAppear(animated)
        timer = NSTimer.scheduledTimerWithTimeInterval(1.0, target: self, selector: "updateTime", userInfo: nil, repeats: true)
    }

    override func viewWillDisappear(animated: Bool) {
        super.viewWillDisappear(animated)
        timer?.invalidate()
    }
}

```

We also need to add a stop watch controller:

```swift
import UIKit

class StopwatchViewController: UIViewController {

    @IBOutlet weak var elapsedTimeLabel: UILabel!

    let dateFormatter = NSDateFormatter()

    var elapsedTimeAtStop: NSTimeInterval = 0
    var dateAtStart: NSDate?
    var timer: NSTimer?

    override func viewDidLoad() {
        dateFormatter.dateFormat = "mm:ss.S"
        dateFormatter.timeZone = NSTimeZone(abbreviation: "UTC")
        updateElapsedTime()
    }

    func updateElapsedTime() {
        elapsedTimeLabel.text = dateFormatter.stringFromDate(dateForFormatter())
    }

    private func dateForFormatter() -> NSDate {
        if let startDate = self.dateAtStart? {
            let intervalSinceStart = NSDate().timeIntervalSinceDate(startDate)
            let totalElapsedTime = elapsedTimeAtStop + intervalSinceStart
            return NSDate(timeIntervalSince1970: totalElapsedTime)
        }
        return NSDate(timeIntervalSince1970: elapsedTimeAtStop)
    }

    @IBAction func startButtonTapped(sender: AnyObject) {
        if dateAtStart == nil {
            dateAtStart = NSDate()
            timer = NSTimer.scheduledTimerWithTimeInterval(1.0/10.0, target: self, selector: "updateElapsedTime", userInfo: nil, repeats: true)
        }
    }

    @IBAction func stopButtonTapped(sender: AnyObject) {
        if let startDate = dateAtStart? { // stop
            elapsedTimeAtStop += NSDate().timeIntervalSinceDate(startDate)
        } else { // reset
            elapsedTimeAtStop = 0
        }

        timer?.invalidate()
        timer = nil
        dateAtStart = nil
        updateElapsedTime()
    }
}
```
<a href="http://imgur.com/j2tg3ul"><img src="http://i.imgur.com/j2tg3ul.gif" title="source: imgur.com" /></a>

## Programatically setting up a tab bar controller
If you have 2 view controllers vc1 and vc2 created programmatically, then you can instantiate a `UITabBarController` and initialize it like this:

```swift
let tabBarController = UITabBarController()
tabBarController.viewControllers = [vc1, vc2]
```

Before we set up tab view controller programmatically, we need to remove the tab view
controller that we added in the Interface Builder (IB).

In IB, remove the segues you have from tab bar view controller to both clock view controller 
and stop watch view controller. After that delete the tab view controller
altogether from IB.

Your story board should look something like this now:

<a href="http://imgur.com/L4MZl1d"><img src="http://i.imgur.com/L4MZl1d.gif" title="source: imgur.com" /></a>

When the app starts up, our AppDelegate's function `didFinishLaunchingWithOptions` 
gets called. This is where we can do any custom view controller set up.

This is sample code from AppDelegate.swift:

```swift
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        // create UIWindow with the same size as main screen
        window = UIWindow(frame: UIScreen.mainScreen().bounds)
        
        // create story board. Default story board will be named as Main.storyboard in your project.
        let storyboard = UIStoryboard(name: "Main", bundle: nil)
        
        // create view controllers from storyboard
        // Make sure you set Storyboard ID for both the viewcontrollers in 
        // Interface Builder -> Identitiy Inspector -> Storyboard ID
        let clockViewController = storyboard.instantiateViewControllerWithIdentifier("ClockViewController")
        let stopWatchViewController = storyboard.instantiateViewControllerWithIdentifier("StopWatchViewController")
        
        // Set up the Tab Bar Controller to have two tabs
        let tabBarController = UITabBarController()
        tabBarController.viewControllers = [clockViewController, stopWatchViewController]
        
        // Make the Tab Bar Controller the root view controller
        window?.rootViewController = tabBarController
        window?.makeKeyAndVisible()

        return true
    }

```

Make sure you provide story board id for both clock and stop watch view controllers
in IB so that they can be initialized in code:

<a href="http://imgur.com/ySOAC6i"><img src="http://i.imgur.com/ySOAC6i.gif" title="source: imgur.com" /></a>

It is very common for a tab bar controller to have navigation controllers as root
view controllers. If this is the case, you will want to assign the storyboard ID
to the navigation controller instead of the view controller directly. You can access
the top view controller in the navigation stack to do any additional configuration.

```swift
let nav = storyboard.instantiateViewController(withIdentifier: "ExampleNavigationController") as! UINavigationController
let vc = nav.topViewController as! ClockViewController
// perform any initial configuration of vc here...
```

## Responding to a tab being selected
_to be completed_

## The "More.." button
The tab bar has limited space for displaying your custom items. If you add six or more 
custom view controllers to a tab bar controller, the tab bar controller displays only 
the first four items plus the standard `More` item on the tab bar. 
Tapping the More item brings up a standard interface for selecting the remaining items.

```swift
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        window = UIWindow(frame: UIScreen.mainScreen().bounds)
        
        let vc1 = setUpViewController("First", backgroundColor: UIColor.orangeColor())
        let vc2 = setUpViewController("Second", backgroundColor: UIColor.purpleColor())
        let vc3 = setUpViewController("Third", backgroundColor: UIColor.redColor())
        let vc4 = setUpViewController("Fourth", backgroundColor: UIColor.greenColor())
        let vc5 = setUpViewController("Fifth", backgroundColor: UIColor.blueColor())
        let vc6 = setUpViewController("Sixth", backgroundColor: UIColor.yellowColor())
        
        // Set up the Tab Bar Controller
        let tabBarController = UITabBarController()
        tabBarController.viewControllers = [vc1, vc2, vc3, vc4, vc5, vc6]
        
        // Make the Tab Bar Controller the root view controller
        window?.rootViewController = tabBarController
        window?.makeKeyAndVisible()

        return true
    }
    
    func setUpViewController(title: String, backgroundColor: UIColor) -> UIViewController {
        let vc = UIViewController()
        vc.view.backgroundColor = backgroundColor
        vc.tabBarItem.title = title
        vc.tabBarItem.image = UIImage(named: "star")
        return vc
    }
}
```

In More tab, user can tap on Edit button. That will allow user to reorder
tabs so that they can pick the which 4 view controllers will appear on the tab view
and which view controllers will be in More tab.

<a href="http://imgur.com/QEmPU1e"><img src="http://i.imgur.com/QEmPU1e.gif" title="source: imgur.com" /></a>

### Configuring what is allowed to be reordered
By default, the user is allowed to rearrange all items on the tab bar.
You can specify what view controllers user can rearrange by setting
the `customizableViewControllers` property.
By default, all the items added to tab bar are also added to `customizableViewControllers`.

If we don't want to allow user to change the view controllers for the 
first 2 tabs, then we can set customizableViewControllers to the other
4 view controllers:

```swift
let tabBarController = UITabBarController()
tabBarController.viewControllers = [vc1, vc2, vc3, vc4, vc5, vc6]
tabBarController.customizableViewControllers = [vc3, vc4, vc5, vc6]
```

In More tab -> Edit, user can now only see and reorder 3rd, 4th, 5th, 6th tabs.

- add screenshot -

## Customizing the appearance of the tab bar
_to be completed_