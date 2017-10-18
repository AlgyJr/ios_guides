_Updated with iOS 10.3, Xcode 8.3.3 and Swift 3_

## Overview
<a href="https://imgur.com/7STmvxL"><img src="https://i.imgur.com/7STmvxL.gif" title="source: imgur.com" /></a>

Navigation controllers help a user browse through a hierarchy of content
by maintaining a history of view controllers that navigated to the
current one.  This quickstart covers the basic procedure for setting up
and using a navigation controller.

## Storyboard setup

### Step 1: Embed root view controller inside a navigation controller
In your storyboard, select the initial view controller in your
hierarchy.  With this view controller selected, choose the menu item
`Editor -> Embed In -> Navigation Controller`.

### Step 2: Add components to control the navigation
Typically navigating to another view controller happens in response to
some user interaction.  You'll have to add some controls (e.g. buttons,
gesture recognizers, table views) to your root view controller to allow
the user to trigger the navigation.

In our demo example below we have added two buttons "Red" and "Blue"
that will trigger a navigation to new view controllers with red and blue
backgrounds.

### Step 3: Create a segue to navigate to a new view controller
Add the view controller you want to navigate to in the storyboard.  You
can trigger navigation in response to an action by control-dragging
from the component that would fire the action to the new view controller
and selecting `show` under `Action Segues`.  This defines a transition
between the two view controllers called a _segue_.

In the example below we create two segues, one in response to a tap on
the "Red" button and the other in response to a tap on the "Blue"
button.  This can be done for a wide variety of events.  One typical
example is handling the row selection event in a table view by
control-dragging from a prototype cell.

<a href="https://imgur.com/Tq6grSl"><img src="https://i.imgur.com/Tq6grSl.gif" title="source: imgur.com" /></a>

### Step 4: Set an identifier for the segue
We'll need a way figure out which segue is firing when we write the code
to configure the new view controller.  You can set a unique identifier for each
segue in the Attributes Inspector after selecting it.

<a href="https://imgur.com/sKRkV9n"><img src="https://i.imgur.com/sKRkV9n.gif" title="source: imgur.com" /></a>

### Step 5: Prepare view controller before navigating to it

To configure the new view controller to which we are navigating, we can
override the [`prepareForSegue`][prepareforsegue] method in the current
view controller.  In this case, we check which segue was triggered and
change the background color of the destination view controller
appropriately.

[prepareforsegue]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/prepareForSegue:sender:

```swift
class ViewController: UIViewController {
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        let destinationVC = segue.destinationViewController as UIViewController
        if segue.identifier == "redSegue" {
            destinationVC.title = "Red"
            destinationVC.view.backgroundColor = UIColor.red
        } else if segue.identifier == "blueSegue" {
            destinationVC.title = "Blue"
            destinationVC.view.backgroundColor = UIColor.blue
        }
    }
}
```

Running this example gives us:

<a href="https://imgur.com/msRxYYM"><img src="https://i.imgur.com/msRxYYM.gif" title="source: imgur.com" /></a>

## Programmatic setup
You can also set up a navigation controller programmatically.  If you
need the navigation controller to be your root view controller the best
place to do this is in the [app delegate](Application-Architecture#programatically-setting-the-root-view-controller).
We'll reimplement the same example as above.

### Step 1: Instantiate the root view controller for the navigation controller
In this case we load the color picker view controller from our example
above.  In a non-storyboard application we would instantiate a view
controller here directly or by loading a nib with
[`initWithNibName`][initwithnibname].

[initwithnibname]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/#//apple_ref/occ/instm/UIViewController/initWithNibName:bundle:

```swift
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        let storyboard = UIStoryboard(name: "Main", bundle: nil)
        let pickColorVC = storyboard.instantiateViewController(withIdentifier: "PickAColor") as UIViewController
        ...
    }
    ...
}
```

### Step 2: Create navigation controller with root view controller
Here we create a navigation controller and provide it with the color
picker view controller as its root view controller.  We also set the
root view controller of the window object to be the navigation
controller.

```swift
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        ...
        let navigationController = UINavigationController(rootViewController: pickColorVC)

        window = UIWindow(frame: UIScreen.main.bounds)
        if let window = window {
          window.rootViewController = navigationController
          window.makeKeyAndVisible()
        }
        return true
    }
    ...
}
```

### Step 3: Respond to events by pushing new view controllers
Here we respond to button taps by configuring a new view controller and
pushing it onto the navigation stack with [`pushViewController`][pushvc].

[pushvc]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationController_Class/index.html#//apple_ref/occ/instm/UINavigationController/pushViewController:animated:

```swift
class ColorPickerViewController: UIViewController {

    @IBAction func didTapRedButton(sender: Any) {
        pushViewController(title: "Red", color: UIColor.red)
    }

    @IBAction func didTapBlueButton(sender: Any) {
        pushViewController(title: "Blue", color: UIColor.blue)
    }

    private func pushViewController(title: String, color: UIColor) {
        let vc = UIViewController()
        vc.view.backgroundColor = color
        vc.title = title
        self.navigationController?.pushViewController(vc, animated: true)
    }
}
```

## Further reading
* [Our navigation controller guide](Navigation-Controller#) provides a
  more in depth look at navigation controllers
* [Apple's navigation controller guide][appleguide] covers some of the more
  technical aspects of navigation controllers

[appleguide]: https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/NavigationControllers.html
