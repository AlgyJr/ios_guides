<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Overview](#overview)
- [Using tab bar controllers in storyboards](#using-tab-bar-controllers-in-storyboards)
  - [Adding tabs to the tab bar controller](#adding-tabs-to-the-tab-bar-controller)
- [Programatically setting up a tab bar controller](#programatically-setting-up-a-tab-bar-controller)
- [Responding to a tab being selected](#responding-to-a-tab-being-selected)
- [The "More.." button](#the-more-button)
  - [Configuring what is allowed to be reordered](#configuring-what-is-allowed-to-be-reordered)
- [Customizing the appearance of the tab bar](#customizing-the-appearance-of-the-tab-bar)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Overview

<!-- TODO insert example gif here -->

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

<!--- TODO add image of setting initial view controller -->

### Adding tabs to the tab bar controller
We can add more tabs by dragging a new view controller frm
the Object Library onto the storyboard and then control-dragging from
the tab bar controller to our the new view controller and then selecting
`Relationship Segues -> view controllers`.  This will add the new view
controller to the tab bar controller's array of view controllers. It
also automatically adds a tab bar item to our new view controller which
will alow us to configure the appearance of the button that represents
this view controller in the tab bar.

<!--- TODO: Insert example of this action here -->

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

<!-- TODO add image with custom class set-->

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

<!-- TODO add image with custom class set-->

## Programatically setting up a tab bar controller
_to be completed_

## Responding to a tab being selected
_to be completed_

## The "More.." button
_to be completed_
### Configuring what is allowed to be reordered
_to be completed_

## Customizing the appearance of the tab bar
_to be completed_
