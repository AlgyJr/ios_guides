<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
- [Overview](#overview)
- [View controller life cycle and events](#view-controller-life-cycle-and-events)
- [Example: implementing a custom drop down menu](#example-implementing-a-custom-drop-down-menu)
  - [Creating a nib and custom view controller class](#creating-a-nib-and-custom-view-controller-class)
  - [Maintaining a list of view controllers](#maintaining-a-list-of-view-controllers)
  - [Adding and removing child view controllers](#adding-and-removing-child-view-controllers)
  - [Showing and hiding the menu](#showing-and-hiding-the-menu)
  - [Displaying and handling selection of menu items](#displaying-and-handling-selection-of-menu-items)
- [Using custom container view controllers](#using-custom-container-view-controllers)
  - [Relationship segues don't exist for custom container view controllers](#relationship-segues-dont-exist-for-custom-container-view-controllers)
  - [Setting up in the AppDelegate](#setting-up-in-the-appdelegate)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Overview
Traditionally in iOS a [view controller](#Application-Architecture#view-controllers)
corresponded to one "screen" in
the application.  To help developers manage multiple view controllers
and navigate between them Apple provided a few [container view
controllers][containercatalog].  The most prominent examples of these
were the [navigation controller](Navigation-Controller#) and [tab bar
controller](Tab-Bar-Controller-Guide#).

[containercatalog]: https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Introduction.html

However (since iOS 5) you can also define your own custom container view
controllers.  This allows great flexibility in the design of your user
interface.  It also encourages you to break up what previously might
have been very large view controllers, managing multiple aspects of an
application, into smaller view controllers with a coherent logical
responsibility in the app.

## View controller life cycle and events
Aside from encapsulating a logical portion of the application and
managing the related events between the models and views, a view
controller is also responsible for responding to events it receives from
the system.  These include life cycle events like `viewDidLoad`,
`viewWillAppear`, `viewDidDisappear`, etc.  They also include changes in
the associated view's size (say due to a change in the orientation of
the device) such as
[`viewWillTransitionToSize`](viewwilltransitiontosize).  When
implementing a custom container view controller we must be careful to
forward these events to any child view controllers.

[viewwilltransitiontosize]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIContentContainer_Ref/index.html#//apple_ref/occ/intfm/UIContentContainer/viewWillTransitionToSize:withTransitionCoordinator:

## Example: implementing a custom drop down menu
To demonstrate how to implement a custom container view controller,
we'll implement a simple drop down menu that allows the user to switch
to any view controller in the list.

<a href="http://imgur.com/LbIigQw"><img src="http://i.imgur.com/LbIigQw.gif" title="source: imgur.com" /></a>

### Creating a nib and custom view controller class
We start out by creating a new subclass of `UIViewController` called
`MenuViewController`.  We do this by selecting `File -> New -> File...
-> Source -> Cocoa Touch Class`.  Here we also create the associated
[nib file](Custom-Views#nibs-and-how-they-are-loaded) by marking the `Also create
XIB file` in the wizard.

Now we can open up our `MenuViewController.xib` and lay out our views.
We add a navigation bar, and "Menu" bar button item.  We add a blank
view (colored pink below) as a container for the views of our child view
controllers.  Finally we add the table view that will display our menu.
Each cell in this table will be an item the user can select in the menu.

<a href="http://imgur.com/MlrDQDm"><img src="http://i.imgur.com/MlrDQDm.png" title="source: imgur.com" /></a>

Next we set the custom class of the [file's
owner](Custom-Views#the-files-owner-object) object to
`MenuViewController` and create these outlets from the file's owner
using the assistant editor (tuxedo view).  We also create an `@IBAction`
to handle the event of the menu button being tapped.

```swift
class MenuViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
    @IBOutlet weak var tableView: UITableView!
    @IBOutlet weak var activeViewContainer: UIView!
    @IBOutlet weak var navItem: UINavigationItem!
    @IBOutlet weak var tableViewHeightConstraint: NSLayoutConstraint!
    ...
    @IBAction func didTapMenuButton(sender: AnyObject) {
    }
    ...
}
```

### Maintaining a list of view controllers
We'll have to be able to maintain a list of view controllers that are
available in the menu.  We'll keep an private array
`viewControllerArray` and will allow consumers to set this array via the
`viewControllers` property.  To keep the implementation simple we won't
support dynamically modifying the `viewControllers` array by inserting
into the array.  Calling the getter will return an immutable copy.
Finally we reset the `activeViewController` if `viewControllers` is
set to a new array that does not contain the current
`activeViewController`.

```swift
class MenuViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
    ...
    private var viewControllerArray: [UIViewController] = []

    var viewControllers: [UIViewController]  {
        get { // getter returns read only copy
            let immutableCopy = viewControllerArray
            return immutableCopy
        }
        set {
            viewControllerArray = newValue

            // set the active view controller to the first one in the new array if the current one is not in there
            if activeViewController == nil || viewControllerArray.index(of:activeViewController!) == nil {
                activeViewController = viewControllerArray.first
            }
        }
    }
    ...
}
```

### Adding and removing child view controllers
In order to keep track of which view controller is currently being
displayed, we'll maintain a `activeViewController` variable.  When this
variable is set we'll remove the previous view controller's view and
swap in the new active view controller's view and set the navigation bar
title to the the new active view controller's title.

In order for the view controller's life cycle events and other system
events to propagate properly we have to call
[`addChildViewController`][addchildviewcontroller] before adding a child
view controller's view as a subview of our view.  To notify the child
view controller that we finished adding its view to the view hierarchy
we have to call [`didMoveToParentViewController`][didmovetoparent]
afterwards.

Likewise when removing a child view controller's view from the
hierarchy, we first have to notify it we will do this by calling
[`willMoveToParentViewController`][willmovetoparent] with `nil` as the
new parent view controller.  After we are done removing the child view
we have to also remove its view controller from our set of child view
controllers by calling its
[`removeFromParentViewController`][removefromparent] method.

More information on child view controller management and implementing
custom view controlers can be found in [guide from Apple][applecustomvcguide].

[willmovetoparent]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/willMoveToParentViewController:
[didmovetoparent]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/didMoveToParentViewController:
[addchildviewcontroller]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/addChildViewController:
[removefromparent]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/removeFromParentViewController
[applecustomvcguide]: https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/ImplementingaContainerViewController.html

```swift
class MenuViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
    ...
    private var activeViewController: UIViewController? {
        didSet {
            removeInactiveViewController(oldValue)
            updateActiveViewController()
        }
    }

    private func removeInactiveViewController(inactiveViewController: UIViewController?) {
        if isViewLoaded() {
            if let inActiveVC = inactiveViewController {
                inActiveVC.willMoveToParentViewController(nil)
                inActiveVC.view.removeFromSuperview()
                inActiveVC.removeFromParentViewController()
            }
        }
    }

    private func updateActiveViewController() {
        if isViewLoaded() {
            if let activeVC = activeViewController {
                addChildViewController(activeVC)
                activeVC.view.frame = activeViewContainer.bounds
                activeViewContainer.addSubview(activeVC.view)
                navItem.title = activeVC.title
                activeVC.didMoveToParentViewController(self)
            }
        }
    }
    ...
}
```

### Showing and hiding the menu
We use the height constraint on `tableView` to show and hide our menu.
We hide it initially in `viewDidLoad` and then animate it open/close
every time the menu button is tapped.

```swift
class MenuViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
    ...
    // MARK: view did load
    override func viewDidLoad() {
        super.viewDidLoad()
        tableView.dataSource = self
        tableView.delegate = self
        tableView.registerClass(UITableViewCell.self, forCellReuseIdentifier: "TableViewCell")
        tableView.rowHeight =  50
        // menu is hidden to start
        self.tableViewHeightConstraint.constant = 0
        updateActiveViewController()
    }

    // MARK: menu button handler
    @IBAction func didTapMenuButton(sender: AnyObject) {
        if (tableViewHeightConstraint.constant == 0) {
            showMenu()
        } else {
            hideMenu()
        }
    }

    private func hideMenu() {
        UIView .animateWithDuration(0.3, animations: { () -> Void in
            self.tableViewHeightConstraint.constant = 0
            self.tableView.layoutIfNeeded()
        });
    }

    private func showMenu() {
        UIView .animateWithDuration(0.3, animations: { () -> Void in
            let totalHeight = self.tableView.rowHeight * CGFloat(self.tableView.numberOfRowsInSection(0))
            self.tableViewHeightConstraint.constant = totalHeight
            self.tableView.layoutIfNeeded()
        });
    }
    ...
}
```

### Displaying and handling selection of menu items
Finally we use our table view data source and delegate methods to
populate our menu table with one cell for each view controller in the
`viewControllerArray`.  When the user selects a cell we set the
corresponding view controller to be the active view controller and hide
the menu.  Notice that the observer on `activeViewController` above will do the
swapping in/out of the child view controllers for us.

```swift
class MenuViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
    ...
    // MARK: view controller delegates
    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return viewControllerArray.count
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCellWithIdentifier("TableViewCell", forIndexPath: indexPath) as UITableViewCell
        cell.textLabel?.text = viewControllerArray[indexPath.row].title
        return cell
    }

    func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
        tableView.deselectRowAtIndexPath(indexPath, animated: true)
        activeViewController = viewControllerArray[indexPath.row]
        hideMenu()
    }
}
```

## Using custom container view controllers
### Relationship segues don't exist for custom container view controllers
If you are familiar with built-in container view controllers such as the
[navigation controller](Navigation-Controller#) and [tab bar controller](Tab-Bar-Controller-Guide#),
you'll know that in order to use them in a storyboard you have to create
a _relationship segue_ (e.g. to the navigation controller's root view
controller).  Unfortunately there is no way to create a custom
relationship segue for your custom container view controllers.

This means that is difficult to set custom container view controllers as the
root view of your storyboard.  We can try something like implementing
`initWithCoder` to load our nib and then setting the custom class of
a view controller in the storyboard (see the [custom views
guide](Custom-Views#) for more information on how nibs are loaded).

```swift
class MenuViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
    ...
    required init(coder aDecoder: NSCoder) {
        super.init(nibName: "MenuViewController", bundle: nil)
    }
}
```

However because there is no way to create relationship segues, the only
place to initialize the other view controllers that will be in our
`viewControllers` array is in `viewDidLoad`.  However, if we want to
make our `MenuViewController` reusable, this clearly breaks
encapsulation.

### Setting up in the AppDelegate
One way we can solve this problem is to manually set up the root view
controller in the
[AppDelegate](Application-Architecture#the-entry-point-to-your-application)
(the way it was done before storyboards existed).  This can be done as
follows.

```swift
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        let vc1 = UIViewController()
        let vc2 = UIViewController()
        let vc3 = UIViewController()

        vc1.view.backgroundColor = UIColor.redColor()
        vc2.view.backgroundColor = UIColor.greenColor()
        vc3.view.backgroundColor = UIColor.blueColor()

        vc1.title = "One"
        vc2.title = "Two"
        vc3.title = "Three"

        let menuViewController = MenuViewController(nibName: "MenuViewController", bundle: nil)

        // the window object is already created for us since this is a storyboard app
        // we would have to initialize this manually in non-storyboard apps
        window?.rootViewController = menuViewController

        menuViewController.viewControllers = [vc1, vc2, vc3]
        return true
    }
    ...
}
```

Notice that we instantiated our `MenuViewController` by calling
[`initWithNibName`][initwithnibname].  We can also load in other view
controllers (say the items in our menu) this way.  Since this was a demo
app we just instantiated the child view controllers as instances of
`UIViewController`.  If we had needed to instantiate a view
controller from the storyboard we could have done something like this

```swift
      let storyboard = UIStoryboard(name: "Main", bundle: nil)
      let viewController = storyboard.instantiateViewControllerWithIdentifier("IdentifierThatWasSetInStoryboard") as MyViewControllerClass
```

[initwithnibname]: https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/ViewLoadingandUnloading/ViewLoadingandUnloading.html#//apple_ref/doc/uid/TP40007457-CH10