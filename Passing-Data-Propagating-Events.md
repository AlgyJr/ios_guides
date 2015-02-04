<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Overview](#overview)
- [Basic example](#basic-example)
- [Using segues in a storyboard](#using-segues-in-a-storyboard)
- [The delegate pattern in iOS](#the-delegate-pattern-in-ios)
- [Passing blocks](#passing-blocks)
- [The target-action pattern](#the-target-action-pattern)
- [Key-Value observing](#key-value-observing)
- [Broadcasting messages with `NSNotificationCenter`](#broadcasting-messages-with-nsnotificationcenter)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Overview

While your application runs, as [events are triggered and
processed][maineventloop], you'll need a way for objects in your
application to propagate these events and to get the data they need from
each other in order to respond properly.

[maineventloop]: https://developer.apple.com/library/ios/documentation/General/Conceptual/Devpedia-CocoaApp/MainEventLoop.html

In iOS, there are quite a few standard ways to pass data and propagate
events between objects:

* segues in storyboards
* delegate pattern
* passing blocks (closures) around
* target-action pattern
* key-value observation
* a publish/subscribe message bus with `NSNotificationCenter`

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


<!-- TODO: for each type of thing we need to link to example in apple api -->

## Using segues in a storyboard

When working in a project with storyboards, the most common way to
transition two view controllers is via a _segue_ (pronounced seg-way).
Segues are a way to create and visualize the transition inside your
storyboard.  They provide a way to initialize the destinationView
controller in [`prepareForSegue`][prepareforsegue].  They also provide
for a way to propagate events and information back to the original
source view controller via [unwind segues][unwindsegues].

[unwindsegues]: https://developer.apple.com/library/ios/technotes/tn2298/_index.html
[prepareforsegue]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/prepareForSegue:sender:

We create our [example app](#basic-example) by starting with `Single
View Application` project template.  We add a single button "Open Color
Picker" to the root view controller `ViewController`. We add a second
view controller with custom class `ColorPickerViewController` with an
`@IBOutlet` for the segmented control that will contain our color
choices.

In the storyboard we create a modal segue from the "Open Color Picker" button
to the color picker view controller by control-dragging.

We also create an _unwind segue_ by control dragging from the "Done"
button to the red exit door above the color picker view controller.  An
unwind segue is a way for a view controller to respond to an event by
navigating back to the view controller that caused it to be loaded.  In
order for any unwind segues to be available we must first add an
`@IBAction` method to the original source view controller that will be
_unwound to_ taking a single `UIStoryboardSegue` parameter.  In our case
this means we added the `didPickColorUnwind` method to the
`ViewController` class (see code below).

<a href="http://imgur.com/FTwy73j"><img src="http://i.imgur.com/FTwy73j.gif" title="source: imgur.com" /></a>

In order to able to idenfy a storyboard segue in our class code we must
provide it with an identifier by selecting the segue and using the
Attributes Inspector.

<a href="http://imgur.com/0kwo5vS"><img src="http://i.imgur.com/0kwo5vS.png" title="source: imgur.com" /></a>

In our source view controller `ViewController` we prepare for the segue
by configuring the color picker view controller's initally selected
color with our current background color.  Our
`ColorPickerViewController` provides us with a way to obtain the
currently selected color when the unwind segue is triggered with the
`colorFromSelection` method.  We set the selected color be our
background color when the unwind segue happens.

```swift
class ViewController: UIViewController {
    override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
        if segue.identifier == "OpenColorPickerSegue" {
            let colorPickerVC = segue.destinationViewController as ColorPickerViewController
            colorPickerVC.startingColor = view.backgroundColor
        }
    }

    @IBAction func didPickColorUnwind(segue: UIStoryboardSegue) {
        let colorPickerVC = segue.sourceViewController as ColorPickerViewController
        if let selectedColor = colorPickerVC.colorFromSelection() {
            view.backgroundColor = selectedColor
        }
    }
}
```

In `ColorPickerViewController` initialize our segmented control with our
color choices in `viewDidLoad`.  We also sync the selected segment on
our segmented control with the inital `startingColor`.

The `colorFromSelection` allows the unwind segue handler in
`ViewController` to obtain the currently selected color.

```swift
class ColorPickerViewController: UIViewController {

    @IBOutlet weak var colorsSegmentedControl: UISegmentedControl!

    let colors = [("Cyan", UIColor.cyanColor()),  ("Magenta", UIColor.magentaColor()), ("Yellow", UIColor.yellowColor())]

    var startingColor: UIColor?

    override func viewDidLoad() {
        super.viewDidLoad()

        // initalize segmented control and select the starting color if it is one of our segments
        colorsSegmentedControl.removeAllSegments()
        var selectedIndex = UISegmentedControlNoSegment
        for (index, color) in enumerate(colors) {
            if color.1.isEqual(startingColor) {
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
}
```

For another example of working with segues see our
[[navigation controller guide|Navigation Controller]].

## The delegate pattern in iOS
Since segues are only available in storyboards, we'll need a different
way to coordinate the interaction between view controllers in a
non-storyboard application.  One very common method to do this in iOS is
the [delegate pattern].
[delegatepattern]: https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html

The delegate pattern works by having a _delegating_ object maintain a
reference to another _delegate_ object that is able to provide data and
handle events in cases it is inconvenient for the delegating object to
do so.  A typical use of this to coordinate events between multiple
view-controllers or between view controllers and views is as folows:

1. A view controller `VC1` instantiates another view controller (or
   view) `V2` that won't have all the information it needs to configure
itself (or wants to propagate events to the original view controller).
2. `VC1` implements a delegate protocol corresponding to the `V2` and
   sets itself as `V2`'s delegate
3. `V2` may call its delegate (in this case `VC1`) to obtain data it
   needs to show itself on the screen
4. `V2` may respond to events by calling delegate methods and thus
   propagating the event/offloading the responsiblity to `VC1`.

You probably have already seen this pattern in action with
[`UITableViewDataSource` and `UITableViewDelegate`](Table-View-Guide#the-datasource-and-delegate-properties).

The delegate pattern is ubiquitous throughout the iOS frameworks and
libraries.  It is useful for creating a well defined (compile-time)
interface for communication between objects.  You might also prefer it
if there are many different kinds of messages that you might want to
pass between objects.

We modify our [example](#basic-example) from above to use the delegate
pattern as follows.  We remove the segues from our story board, and have
the main `ViewController` instantiate and present the
`ColorPickerViewController` manually.

Importantly, we also have the `ViewController` class implement
`ColorPickerDelegate` (see next code block) and set itself as the color
picker view controller's delegate before presenting it to the user.  We
implement the delegate method `didPickColor` to respond by setting the
our background color to the selected color and dismissing the color
picker view controller.  Finally we provide our background color as the
initial color by implementing the delegate method `initialColor`.

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

    func initialColor() -> UIColor? {
        return view.backgroundColor
    }
}
```

Our `ColorPickerDelegate` has two methods.  The `didPickColor` method
lets `ColorPickerViewController` notify its delegate when the user has
selected a color by tapping on the "Done" button.  The `initialColor`
color method lets the delegate inform `ColorPickerViewController` of
which color to set as its initially selected color in `viewDidLoad`.

```swift
protocol ColorPickerDelegate: class {
    func colorPicker(picker: ColorPickerViewController, didPickColor color:UIColor?)
    func initialColor() -> UIColor?
}

class ColorPickerViewController: UIViewController {
    @IBOutlet weak var colorsSegmentedControl: UISegmentedControl!

    let colors = [("Cyan", UIColor.cyanColor()),  ("Magenta", UIColor.magentaColor()), ("Yellow", UIColor.yellowColor())]
    weak var delegate: ColorPickerDelegate?

    override func viewDidLoad() {
        super.viewDidLoad()

        // initalize segmented control and select the starting color if it is one of our segments
        colorsSegmentedControl.removeAllSegments()
        var selectedIndex = UISegmentedControlNoSegment
        let initialColor = delegate?.initialColor()

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
        delegate?.colorPicker(self, didPickColor: colorFromSelection())
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

```
class ViewController: UIViewController {
    @IBAction func openColorPickerTapped(sender: AnyObject) {
        let storyboard = UIStoryboard(name: "Main", bundle: NSBundle.mainBundle())
        let colorPickerVC = storyboard.instantiateViewControllerWithIdentifier("ColorPicker") as ColorPickerViewController
        colorPickerVC.initialColor = view.backgroundColor
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
    var initialColor: UIColor?
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
the following code tells the `button` to call `myObject.myMethod()` when
it is tapped.

```swift
let button = UIButton()
button.addTarget(myObject, action: "myMethod", forControlEvents: .TouchUpInside)
// add button to view hierarchy
```

In Swift, it is not possible to implement a method that can _accept_ a
target-action since `NSObject`'s [`performSelector`][performselector]
methods and the [`NSInvocation`][nsinvocation] APIs are disabled in
Swift.  This was because these APIs do not allow the compiler to check
for type safety.  Another downside is that its is not easy to use the
target-action pattern to invoke methods that require more than two
parameters.

[nsinvocation]: https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSInvocation_Class/index.html
[performselector]: https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Protocols/NSObject_Protocol/index.html#//apple_ref/occ/intfm/NSObject/performSelector:
[targetaction]: https://developer.apple.com/library/ios/documentation/General/Conceptual/Devpedia-CocoaApp/TargetAction.html

_TODO: rewrite example in Objective-C to use target-action pattern_

## Key-Value observing
iOS provides a way for any object to listen to changes in another
object's properties via a powerful mechanism known as [Key-Value
observing (KVO)][kvo].  The basic outline for using KVO is

1. Register an object for observation by calling `addObserver:forKeyPath:`
2. Implement `observeValueForKeyPath` in the class doing the observing
3. Update properties in object being observed with the corresponding
   `keyPath`.

[kvo]: https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html

In our [running example](#basic-example), we have `ViewController`
observe the `selectedColor` property on our `ColorPickerViewController`.
We set up the observation when initializing the
`ColorPickerViewController` by adding ourself (`ViewController`) as an
observer.

When this property is set, we know that the user has finished selecting
a color.  We then remove the observer and call `didPickColor` to set the
background color and dismiss the `ColorPickerViewController`.

__It is important that we remove the observer once we are done.__
Failure to properly remove observers may cause unexpected behavior or
your app to crash.

```swift
private var colorPickerObservationContext = 0
private let selectedColorKeyPath = "selectedColor"

class ViewController: UIViewController {

    @IBAction func openColorPickerTapped(sender: AnyObject) {
        let storyboard = UIStoryboard(name: "Main", bundle: NSBundle.mainBundle())
        let colorPickerVC = storyboard.instantiateViewControllerWithIdentifier("ColorPicker") as ColorPickerViewController
        colorPickerVC.initialColor = view.backgroundColor
        colorPickerVC.addObserver(self, forKeyPath: selectedColorKeyPath, options: .New, context: &colorPickerObservationContext)
        presentViewController(colorPickerVC, animated: true, completion: nil)
    }

    func didPickColor(color: UIColor?) {
        if let selectedColor = color {
            view.backgroundColor = selectedColor
        }
        dismissViewControllerAnimated(true, completion: nil)
    }

    override func observeValueForKeyPath(keyPath: String, ofObject object: AnyObject, change: [NSObject : AnyObject], context: UnsafeMutablePointer<Void>) {
        if context == &colorPickerObservationContext && keyPath == selectedColorKeyPath
            && object is ColorPickerViewController {

            let colorPickerVC = object as ColorPickerViewController
            colorPickerVC.removeObserver(self, forKeyPath: selectedColorKeyPath, context: &colorPickerObservationContext)

            let selectedColor = change[NSKeyValueChangeNewKey] as UIColor
            didPickColor(selectedColor)
        }
    }
}
```

In `ColorPickerViewController` we never explicitly call any method or
invoke any closure that refers directly to `ViewController`.  We simply
introduce a property `selectedColor` that is only set once the user taps
on the "Done" button.  We already registered the `ViewController` as
listener to changes in this property above.  Any change will then
automatically trigger the `observeValueForKeyPath` method in
`ViewController`.

```swift
class ColorPickerViewController: UIViewController {
    @IBOutlet weak var colorsSegmentedControl: UISegmentedControl!

    let colors = [("Cyan", UIColor.cyanColor()),  ("Magenta", UIColor.magentaColor()), ("Yellow", UIColor.yellowColor())]
    var initialColor: UIColor?
    dynamic var selectedColor: UIColor?

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
        selectedColor = colorFromSelection()
    }
}
```


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

