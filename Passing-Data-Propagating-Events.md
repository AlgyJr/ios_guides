## Overview

While your application runs, as [events are triggered and
processed][maineventloop], you'll need a way for objects in your
application to propagate these events and to get the data they need from
each other in order to respond properly.

[maineventloop]: https://developer.apple.com/library/ios/documentation/General/Conceptual/Devpedia-CocoaApp/MainEventLoop.html

In iOS, there are quite a few standard ways to pass data and propagate
events between objects:

* Delegate pattern
* Passing blocks (closures) around
* Target-Action pattern
* Publish/Subscribe message bus with `NSNotificationCenter`

This guide gives an high-level overview of each of these mechanisms with
a focus on passing data and propagating events between two different
view controllers and between view controllers and views.  Generally when
working with view controllers and views, the following steps are typical

1. A view controller `VC1` configures a view or another view controller
   `V2`.  This can be done by calling an initializer or by
obtaining a reference to and setting properties on `V2`.
2. The second view or view controller `V2` is loaded and shown on the
   screen.  Additionally `V2` may request more information from `VC1`
while it's on the screen.
3. The user triggers an event in `V2` that needs to be handled in `VC1`.
   This means that `V2` will need to propagate the event and possibly
send some information about it's current state to `VC1`.

## Basic example

To demonstrate this basic use case, we'll use a standard example
throughout. We'll build a demo app that lets the user change the
background color of the main view controller by opening up a secondary
color picker view controller.

<a href="http://imgur.com/XgIWavY"><img src="http://i.imgur.com/XgIWavY.gif" title="source: imgur.com" /></a>

## Delegate Pattern

The [delegate pattern](https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html) is a common pattern in iOS. It can be used to communicate events between multiple
view controllers, between view controllers and views, etc. If you've implemented a TableView, then you've used the delegate pattern when you implemented [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol) and [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol).

It can be really useful to create your own delegate. To do so, requires the following 4 steps:

1. Create a [protocol](http://guides.codepath.com/ios/Understanding-Swift#protocols) that has methods for each event:

    ```swift
    protocol ColorPickerDelegate: class {
        func colorPicker(picker: ColorPickerViewController, didPickColor color: UIColor?)
    }
    ```
2. In the source class, create a property to hold a reference to the listener.

    ```swift
    class ColorPickerViewController: UIViewController {
        weak var delegate: ColorPickerDelegate?

        // ... Rest of class ...
    }
    ```
3. In the source class, call the appropriate delegate method when an event occurs.

    ```swift
    class ColorPickerViewController: UIViewController {

        weak var delegate: ColorPickerDelegate?

        @IBOutlet weak var colorsSegmentedControl: UISegmentedControl!

        let colors = [("Cyan", UIColor.cyanColor()),  ("Magenta", UIColor.magentaColor()), ("Yellow", UIColor.yellowColor())]

        override func viewDidLoad() {
            super.viewDidLoad()

            // initialize segmented control and select the starting color if it is one of our segments
            colorsSegmentedControl.removeAllSegments()
            var selectedIndex = UISegmentedControlNoSegment

            for (index, color) in colors.enumerate() {
                if color.1.isEqual(initialColor) {
                    selectedIndex = index
                }
                colorsSegmentedControl.insertSegmentWithTitle(color.0, atIndex: index, animated: false)
            }
            colorsSegmentedControl.selectedSegmentIndex = selectedIndex
        }

        func colorFromSelection() -> UIColor? {
            let selectedIndex = colorsSegmentedControl.selectedSegmentIndex
            if selectedIndex != UISegmentedControlNoSegment {
                return colors[selectedIndex].1
            }
            return nil
        }

        @IBAction func doneButtonTapped(sender: AnyObject) {
            delegate?.colorPicker(self, didPickColor: colorFromSelection())
        }
    }
    ```
4. In the listening class, implement the protocol.

    ```swift
    class ViewController: UIViewController, ColorPickerDelegate {
        @IBAction func openColorPickerTapped(sender: AnyObject) {
            let storyboard = UIStoryboard(name: "Main", bundle: NSBundle.mainBundle())
            let colorPickerVC = storyboard.instantiateViewControllerWithIdentifier("ColorPicker") as ColorPickerViewController
            colorPickerVC.delegate = self
            presentViewController(colorPickerVC, animated: true, completion: nil)
        }

        func colorPicker(picker: ColorPickerViewController, didPickColor color: UIColor?) {
            if let selectedColor = color {
                view.backgroundColor = selectedColor
            }
            dismissViewControllerAnimated(true, completion: nil)
        }
    }
    ```

## Passing blocks

[Closures in Swift][swiftclosures] and [blocks in
Objective-C][objectivecblocks] are first class concepts.  This means
that you can pass closures as parameters and assign them to variables to
be exectuted later.  These concepts allow you to implement a basic
callback mechanism that can be used to propagate and pass on the
responsibility of handling an event.

[swiftclosures]: https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Closures.html
[objectivecblocks]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithBlocks/WorkingwithBlocks.html

Passing blocks is common in iOS frameworks and libraries where a single
callback (as opposed to a protocol containing many methods used with
delegates) is required.  It also provides the advantage of being able to
define the closure inline so that it [closes over][closurewikipedia] the
current environment and hence you will have access to any variables that
are currently in scope.

An example of an iOS library that uses blocks is
[`NSURLConnection`](Network-Programming#nsurlconnection).  It allows you
to specify a block that will be executed asynchronously when a network
request returns.  Another example is the
[`NSNotificationCenter.addObserverForName:object:queue:usingBlock:`][notificationaddobserverforname]
method called [below](#broadcasting-messages-with-nsnotificationcenter).

[closurewikipedia]: http://en.wikipedia.org/wiki/Closure_%28computer_programming%29
[notificationaddobserverforname]: https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSNotificationCenter_Class/#//apple_ref/occ/instm/NSNotificationCenter/addObserverForName:object:queue:usingBlock:

One downside to using blocks is that it may be difficult to get
the type definitions correct for complicated closures having optionals,
collection types, or other closures as parameters or return values.

We can see block callbacks in action continuing with our [running
example](#basic-example).  We still have our `ViewController`
instantiate and configure the `ColorPickerViewController`.  The
`ColorPickerViewController` now has a `doneHandler` which we set to be a
block that calls our method `didPickColor`.  We present the
`ColorPickerViewController` to the user after finishing our
configuration.

As before, the method `didPickColor` updates our background color and
dismisses the `ColorPickerViewController`.

```swift
class ViewController: UIViewController {
    @IBAction func openColorPickerTapped(sender: AnyObject) {
        let storyboard = UIStoryboard(name: "Main", bundle: NSBundle.mainBundle())
        let colorPickerVC = storyboard.instantiateViewControllerWithIdentifier("ColorPicker") as ColorPickerViewController
        colorPickerVC.doneHandler = {(color: UIColor?) -> Void in
            self.didPickColor(color)
        }

        presentViewController(colorPickerVC, animated: true, completion: nil)
    }

    func didPickColor(color: UIColor?) {
        if let selectedColor = color {
            view.backgroundColor = selectedColor
        }
        dismissViewControllerAnimated(true, completion: nil)
    }
}
```

In `ColorPickerViewController` we propagate the action of the user
clicking on the "Done" button to `ViewController` by executing the
`doneHandler` that was set.

```swift
class ColorPickerViewController: UIViewController {
    @IBOutlet weak var colorsSegmentedControl: UISegmentedControl!

    let colors = [("Cyan", UIColor.cyanColor()),  ("Magenta", UIColor.magentaColor()), ("Yellow", UIColor.yellowColor())]
    var doneHandler: ((UIColor?) -> Void)?

    override func viewDidLoad() {
        super.viewDidLoad()

        // initalize segmented control and select the starting color if it is one of our segments
        colorsSegmentedControl.removeAllSegments()
        var selectedIndex = UISegmentedControlNoSegment

        for (index, color) in enumerate(colors) {
            if color.1.isEqual(initialColor) {
                selectedIndex = index
            }
            colorsSegmentedControl.insertSegmentWithTitle(color.0, atIndex: index, animated: false)
        }
        colorsSegmentedControl.selectedSegmentIndex = selectedIndex
    }

    func colorFromSelection() -> UIColor? {
        let selectedIndex = colorsSegmentedControl.selectedSegmentIndex
        if selectedIndex != UISegmentedControlNoSegment {
            return colors[selectedIndex].1
        }
        return nil
    }

    @IBAction func doneButtonTapped(sender: AnyObject) {
        doneHandler?(colorFromSelection())
    }
}
```

## The target-action pattern

An another construct used to propagate events in iOS is the
[target-action][targetaction] pattern.  This is an older Objective-C
pattern that allows a class to register a method (_action_ or
_selector_) to be executed on some object (_target_) at some point
later.  This pattern is used throughout many iOS libraries for example
the following code tells the `button` to call `self.onButtonTap()` when
it is tapped.

This pattern is pretty much the same as creating an `@IBAction`
from the interface builder in XCode; however, this is done in code. 
It's used when you have to dynamically create view objects in code 
and add actions to it based on control events.

In the following example, you're creating a button dynamically in
code.

```swift
class CodePathViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let button = UIButton()
        button.addTarget(self, action: "onButtonTap", forControlEvents: .TouchUpInside)
        button.frame = CGRectMake(0, 0, 300, 500)

        //Add button to the view
        self.view.addSubview(letterButton)
    }

    func onButtonTap() {
        print("Button Tapped!")
    }
}
```

Let's take a look at the parameters of `addTarget` method:
* target - (**Who to tell**) The target parameter is the object that is going to respond to the control event.
           In this case, the event is `.TouchUpInside` (Tap). Usually, the target is the
           object of the ViewController class in which the button was created.
           In the example, we reference the object of the ViewController class with the
           keyword `self`. 

* action - (**What to tell them**) The action parameter is simply the name of the method that needs to be invoked in
           the target object.

* forControlEvents - (**When to tell them**) This is where you pass the type of event for your button.
                     Here's a list of [events][events] you can use for UIButton.

To simplify, in the above example, we let the button know that it has to call the instance
method `onButtonTap` of the class `CodePathViewController` when it is tapped.

One downside is that its is not easy to use the target-action pattern to invoke methods 
that require two or more parameters.

[targetaction]: https://developer.apple.com/library/ios/documentation/General/Conceptual/Devpedia-CocoaApp/TargetAction.html
[events]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIControl_Class/index.html#//apple_ref/doc/constant_group/Control_Events

_TODO: rewrite example in Objective-C to use target-action pattern_

## Broadcasting messages with `NSNotificationCenter`
Finally, iOS provides a mechanism for implementing a basic
subcribe/publish message queue via [notification
centers][notificationcenters].  The basic usage is

1. Define an identifer to name this particular type of notification
2. Subscribe to this kind of notification by adding an observer to a
   notification center for notifications with the given name.  You can
instatiate new notification centers, but it is common to use
`NSNotificationCenter.defaultCenter`.
3. Publish notifications to the notification center.  You might include
   additional data in a `userInfo` dictionary.

[notificationcenters]: https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Notifications/Articles/NotificationCenters.html

The `NSNotificationCenter` API is normally used to handle app-wide
events that may be relevant to multiple interested&mdash;possibly
unrelated&mdash;view controllers (e.g. the logged in state of a user).
It is not normally used to pass information between two specific view
controllers.  Nevertheless, we can adapt it our [running
example](#basic-example) as follows.

We define `ColorPickerNotification` as an identifier that will be used
as the name for notifications from our `ColorPickerViewController`.

In `ViewController`, before presenting the `ColorPickerViewController`,
we register an observer for notifications with this name.  In the block
that is triggered when the notification fires, we extract the selected
color from the `userInfo` dictionary and call `didPickColor`.  The
`didPickColor` method sets the background color and dismisses the
`ColorPickerViewController`.

```swift
class ViewController: UIViewController {

    @IBAction func openColorPickerTapped(sender: AnyObject) {
        let storyboard = UIStoryboard(name: "Main", bundle: NSBundle.mainBundle())
        let colorPickerVC = storyboard.instantiateViewControllerWithIdentifier("ColorPicker") as ColorPickerViewController
        colorPickerVC.initialColor = view.backgroundColor

        NSNotificationCenter.defaultCenter().addObserverForName(ColorPickerNotification, object: nil, queue: NSOperationQueue.mainQueue()) { (notification: NSNotification!) -> Void in
            let userInfo = notification?.userInfo
            let selectedColor: UIColor? = userInfo?[ColorPickerSelectedColorKey] as? UIColor
            self.didPickColor(selectedColor)

        }

        presentViewController(colorPickerVC, animated: true, completion: nil)
    }

    func didPickColor(color: UIColor?) {
        if let selectedColor = color {
            view.backgroundColor = selectedColor
        }
        dismissViewControllerAnimated(true, completion: nil)
    }
}
```

In `ColorPickerViewController`, when the "Done" button is tapped we
construct a `userInfo` dictionary containing the currently selected
color.  Then we fire a notification with the key
`ColorPickerNotification` let all subscribers know that the a color has
been picked by the user.

```swift
let ColorPickerNotification = "com.codepath.ColorPickerViewController.didPickColor"
let ColorPickerSelectedColorKey = "com.codepath.ColorPickerViewController.selectedColor"

class ColorPickerViewController: UIViewController {
    @IBOutlet weak var colorsSegmentedControl: UISegmentedControl!

    let colors = [("Cyan", UIColor.cyanColor()),  ("Magenta", UIColor.magentaColor()), ("Yellow", UIColor.yellowColor())]
    var initialColor: UIColor?

    override func viewDidLoad() {
        super.viewDidLoad()

        // initalize segmented control and select the starting color if it is one of our segments
        colorsSegmentedControl.removeAllSegments()
        var selectedIndex = UISegmentedControlNoSegment

        for (index, color) in enumerate(colors) {
            if color.1.isEqual(initialColor) {
                selectedIndex = index
            }
            colorsSegmentedControl.insertSegmentWithTitle(color.0, atIndex: index, animated: false)
        }
        colorsSegmentedControl.selectedSegmentIndex = selectedIndex
    }

    func colorFromSelection() -> UIColor? {
        let selectedIndex = colorsSegmentedControl.selectedSegmentIndex
        if selectedIndex != UISegmentedControlNoSegment {
            return colors[selectedIndex].1
        }
        return nil
    }

    @IBAction func doneButtonTapped(sender: AnyObject) {
        var selectionInfo: [NSObject : AnyObject] = [:]
        if let selectedColor = colorFromSelection() {
            selectionInfo[ColorPickerSelectedColorKey] = selectedColor
        }
        NSNotificationCenter.defaultCenter().postNotificationName(ColorPickerNotification, object: self, userInfo: selectionInfo)
    }
}
```