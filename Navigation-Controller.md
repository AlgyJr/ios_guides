_Updated with iOS 10.3, Xcode 8.3.3 and Swift 3_

## Overview


In a typical iOS project you will create one view controller for each
"screen" in your application.  This means you need a way to coordinate
the interaction of these view controllers and the presentation of their
associated views.

In this lesson we will show you how to
- Take the user to another screen (view controllers) within your application
- Pass data between view controllers
- Create a navigation flow with or without a storyboard

<a href="https://imgur.com/7STmvxL"><img src="https://i.imgur.com/7STmvxL.gif" title="source: imgur.com" /></a>

## Navigation Controller

A common way to manage a group of view controllers that are associated
with a hierarchy of content is to use a
[`UINavigationController`][navigationcontroller].  Navigation
controllers maintain a [stack] of view controllers that are pushed onto and
popped off of the top of the stack as the user browses through the
hierarchy.  Their behavior is somewhat similar to that of a web
browser's history and "back button" functionality.

The navigation controller is one example of a built-in UIKit [container
view controller][containervc].  An in-depth guide by Apple covering
navigation controllers and all other built-in container view controllers
can be found [here][uikitcontainers].

[stack]: http://en.wikipedia.org/wiki/Stack_%28abstract_data_type%29
[containervc]: https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/ImplementingaContainerViewController.html
[navigationcontroller]: https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UINavigationController_Class/index.html
[uikitcontainers]: https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Introduction.html#//apple_ref/doc/uid/TP40011313-CH1-SW1

## Basic use of the navigation controller

This guide covers the most common use cases for navigation controllers
and how to pass data between view controllers managed by a navigation
controller.  To demonstrate this functionality, we will build a demo
application that presents the user with a table of names.  When the user
selects a name in the table we will show a detail view controller that
allows the user to edit the name.

The process for using navigation controllers in storyboard applications
actually quite different than when using them programmatically since the
concept of a _segue_ does not exist outside the storyboard.  We'll
create the example first with storyboards.  Then we'll show how the same
example would be built without using storyboards.

### Using navigation controllers in storyboards

First we'll set up our table to show the list of names.  Starting with
the Xcode `Single View Application` template.  We add a table view to
the initial view controller that Xcode has generated and add a single prototype cell that just uses the built-in `UITableViewCell`
class.  We hook up our Table View's `dataSource` property to finish the
initial setup.  If you are unfamiliar with table views please refer to 
the [[table view guide|Table View Guide]].

<a href="https://imgur.com/WBH4TcV"><img src="https://i.imgur.com/WBH4TcV.png" title="source: imgur.com" /></a>

```swift
import UIKit

class ViewController: UIViewController, UITableViewDataSource {

    let names = ["Brent Berg", "Cody Preston", "Kareem Dixon", "Xander Clark",
        "Francis Frederick", "Carson Hopkins", "Anthony Nguyen", "Dean Franklin",
        "Jeremy Davenport", "Rigel Bradford", "John Ball", "Zachery Norman",
        "Valentine Lindsey", "Slade Thornton", "Jelani Dickson", "Vance Hurley",
        "Wayne Ellison", "Kasimir Mueller", "Emery Pruitt", "Lucius Lawrence",
        "Kenneth Mendez"]

    @IBOutlet weak var tableView: UITableView!

    override func viewDidLoad() {
        tableView.dataSource = self
    }

    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return names.count
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "NameCell", for: indexPath)
        cell.textLabel?.text = names[indexPath.row]
        return cell
    }
}

```

<a href="https://imgur.com/OuawCqi"><img src="https://i.imgur.com/OuawCqil.png" title="source: imgur.com" /></a>

#### Adding the navigation controller and setting the root view controller

Next we'll need to add a navigation controller.  When we instantiate a
navigation controller, we must also set its [_root view
controller_][rootvc].  This is the first view controller that will be
shown when the navigation controller is loaded.  Think of it as the
"home page" in our web browser analogy.

One way to add a navigation controller is by dragging a `Navigation
Controller` from the object library into our storyboard.  You'll notice
that if you do this, Xcode will automatically create a separate view
controller to act as the root view controller.  We'll want to set the
root view controller to the one with our table and delete the other one.
This can be done by control dragging from the navigation controller to
our view controller and selecting the `root view controller`
Relationship Segue.  If you haven't worked with segues before, a segue
(pronounced "seg-way") is a storyboard's representation of a
relationship or transition between different view controllers.  We'll
also need to set the _initial view controller_ to the navigation
controller by dragging the arrow that was pointing to the view
controller with our table.

<a href="https://imgur.com/83IhGlK"><img src="https://i.imgur.com/83IhGlK.gif" title="source: imgur.com" /></a>

In most cases if we have a view controller already set up that we want
to act as our root view controller, we can accomplish all the above
steps simply by selecting the menu item `Editor -> Embed In ->
Navigation Controller`.  Running the app, we can see our navigation
controller's translucent grey navigation bar at the top of the screen.

<a href="https://imgur.com/5WtqwmM"><img src="https://i.imgur.com/5WtqwmMl.png" title="source: imgur.com" /></a>

[rootvc]: https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UINavigationController_Class/index.html#//apple_ref/occ/instm/UINavigationController/initWithRootViewController:

#### Pushing a view controller onto the navigation stack
We'll need to create another view controller class to display and allow
the user to edit an individual name.  We do this by dragging another
view controller from the Object Library onto our storyboard.  We add two
text fields to this view controller to store the first and last parts of
the name the user will be editing.  We create a new subclass of
`UIViewController` called `NameController` and set the new view
controller's `Custom Class` property to be `NameController` in our
storyboard.  Once the custom class is set, we can go ahead and create
outlets in our `NameController` class corresponding to the two text
fields. **Note**: we recommend pressing Enter or Tab after entering
the class value since Xcode may not apply the change if you navigate
out of the text field otherwise.

<a href="https://imgur.com/UgE77Nm"><img src="https://i.imgur.com/UgE77Nm.png" title="source: imgur.com" /></a>

```swift
import UIKit

class NameController: UIViewController {

    @IBOutlet weak var firstNameTextField: UITextField!
    @IBOutlet weak var lastNameTextField: UITextField!
}
```

When the user selects name by tapping on a row in the table, we want to
display the `NameController` by pushing it onto the navigation stack.
This can be done by control-dragging from the prototype cell to our name
controller and selecting `show` under `Selection Segue`.  Once this is
done, notice how our name controller now also shows the grey navigation
bar to indicate that it will be shown as part of the navigation stack.

<a href="https://imgur.com/WqWi2kL"><img src="https://i.imgur.com/WqWi2kL.gif" title="source: imgur.com" /></a>

It is important that we select the `show` segue since this corresponds
to the navigation controller pushing a new view controller onto the
stack.  The other segues correspond to other ways of transitioning to a
new view controller including presenting it modally.

Since we initiated our control-drag from the prototype cell, the
`Selection Segue` group was available.  Selecting a segue under this
group means "respond to the selection event with this transition." Other
events could be available depending on the control that initiates the segue.
For example control-dragging from a button will show possible `Action
Segues`.  Running our application right now shows the following.

<a href="https://imgur.com/CmFK6Bw"><img src="https://i.imgur.com/CmFK6Bw.gif" title="source: imgur.com" /></a>

#### Configuring a view controller before it is pushed
You'll notice at least one problem immediately with the current behavior:
the name we selected is not populated in first and last name fields of
our `NameController`.  We'll need to configure the name controller
before it gets displayed on the screen.  We do this by first providing
an identifier to our selection segue.  Next we write some code  in our
_root view controller_'s [`prepareForSegue`][prepareforsegue] method to
respond should a segue with this identifier be triggered.

[prepareforsegue]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/prepareForSegue:sender:

We give the segue an identifier by selecting the storyboard and using
the Attributes Inspector.  Notice that our prototype cell is also
highlighted to indicate that segue will be trigged by selecting a cell.

<a href="https://imgur.com/Q71yKws"><img src="https://i.imgur.com/Q71yKws.png" title="source: imgur.com" /></a>

In our root view controller's `prepareForSegue` method, we check to see
if the segue that was triggered matches the identifier we gave our segue
in the storyboard.  If it does we know that the segue was trigged in
response a user selecting a cell, and thus the `sender` parameter will
be the cell.  If for example the segue had been triggered by a button
tap, the `sender` would be the button.

The `segue` parameter of `prepareForSegue` gives us access to the
`destinationViewController` so that we can configure the new view
controller before it is displayed.  In this case we know that for this
particular segue the `destinationViewController` will be a
`NameController`.  We'll have to update our `NameController` to have the
ability to set the name and have it displayed properly when the view is
loaded.  Finally, we also deselect the row since we won't want it to be
selected once we go back from the name controller.


```swift
class ViewController: UIViewController, UITableViewDataSource {
    ...
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        if segue.identifier == "showNameControllerSegue" {
            let cell = sender as! UITableViewCell
            if let indexPath = tableView.indexPath(for: cell) {
                let nameController = segue.destination as! NameController
                nameController.fullName = names[indexPath.row]
                tableView.deselectRowAtIndexPath(indexPath, animated: true)
            }
        }
    }
 }
```

```swift
class NameController: UIViewController {
    @IBOutlet weak var firstNameTextField: UITextField!
    @IBOutlet weak var lastNameTextField: UITextField!

    var fullName: String?

    override func viewDidLoad() {
        if let fullName = self.fullName {
            let firstLast = fullName.components(separatedBy: " ")
            firstNameTextField.text = firstLast[0]
            lastNameTextField.text = firstLast[1]
        }
    }
}

```

<a href="https://imgur.com/qWis8Ya"><img src="https://i.imgur.com/qWis8Ya.gif" title="source: imgur.com" /></a>

#### Unwind segues and passing information back up the hierarchy
Next we'll need a way for the user to save any changes to a name back to
our table.  We'll do this by making use of an _unwind segue_.  An unwind
segue is a way for a view controller to respond to an event by
navigating back to the view controller that caused it to be loaded.  In
the the context of navigation controllers, this means the view
controller that is one below the current view controller on the
navigation stack.

First off, we'll need a way for the user to indicate they want to save
the change.  We can add a button to the right side of the navigation bar
in our name controller by dragging a `Navigation Item` and then a `Bar
Button Item` from the Object Library.

<a href="https://imgur.com/ScyeS2O"><img src="https://i.imgur.com/ScyeS2O.gif" title="source: imgur.com" /></a>

To implement an unwind segue we create an `@IBAction` method in the view
controller that will be _unwound to_ taking a single `UIStoryboardSegue`
parameter.  In our case this means we added the below `saveName` method
to our _root view controller_.


```swift
class ViewController: UIViewController, UITableViewDataSource {
    ...
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        if segue.identifier == "showNameControllerSegue" {
            let cell = sender as! UITableViewCell
            if let indexPath = tableView.indexPath(for: cell) {
                let nameController = segue.destination as! NameController
                nameController.fullName = names[indexPath.row]
                tableView.deselectRowAtIndexPath(indexPath, animated: true)
            }
        }
    }

    @IBAction func saveName(segue: UIStoryboardSegue) {
        // add logic here to handle a transition back from the
        // name controller resulting from a user tapping on Save
    }
}
```

Next we'll have to hook up our `Save` button to this segue by
control-dragging from the button to the `Exit` outlet and selecting the
`@IBAction` we just created.  The `Exit` outlet is the red exit door
that appears above of your active view controller in the storyboard.

<a href="https://imgur.com/QusMqmi"><img src="https://i.imgur.com/QusMqmi.gif" title="source: imgur.com" /></a>

Finally we'll need to write the code in our root view controller to
handle updating the name once the `saveName` method is called.

```swift
class ViewController: UIViewController, UITableViewDataSource {

    var names = ["Brent Berg", "Cody Preston", "Kareem Dixon", "Xander Clark",
        "Francis Frederick", "Carson Hopkins", "Anthony Nguyen", "Dean Franklin",
        "Jeremy Davenport", "Rigel Bradford", "John Ball", "Zachery Norman",
        "Valentine Lindsey", "Slade Thornton", "Jelani Dickson", "Vance Hurley",
        "Wayne Ellison", "Kasimir Mueller", "Emery Pruitt", "Lucius Lawrence",
        "Kenneth Mendez"]

    var selectedIndexPath: IndexPath?

    @IBOutlet weak var tableView: UITableView!

    ...

    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        if segue.identifier == "showNameControllerSegue" {
            let cell = sender as! UITableViewCell
            if let indexPath = tableView.indexPath(for: cell) {
                let namesController = segue.destination as! NameController
                namesController.fullName = names[indexPath.row]
                tableView.deselectRowAtIndexPath(indexPath, animated: true)
                selectedIndexPath = indexPath
            }
        }
    }

    @IBAction func saveName(segue: UIStoryboardSegue) {
        let nameController = segue.source as! NameController
        let name = nameController.firstNameTextField.text! + " " + nameController.lastNameTextField.text!

        if let indexPath = selectedIndexPath {
            names[indexPath.row] = name
            tableView.reloadRows(at: [indexPath], with: .automatic)
            selectedIndexPath = nil
        }
    }
}
```

Notice that we were able to obtain a reference to the name controller in
`saveName` by using `segue.sourceViewController`.  This makes sense
because the name controller is the source of the _unwind segue_, whereas
the destination controller is the root view controller.  The other thing
to note is that we had to maintain an additional state variable
`selectedIndexPath` to help us remember which name it was we were
editing.  Putting everything together we get the following
functionality:

<a href="https://imgur.com/t1krDPG"><img src="https://i.imgur.com/t1krDPG.gif" title="source: imgur.com" /></a>

### Using navigation controllers without storyboards

We will now show how the same example would be built in a non-storyboard
application.  Conceptually, some of the steps will be the same.
However, since segues do not exist outside a storyboard, we will have to
set up more code ourselves to handle events and interface between view
controllers.

As above we'll first set up our table to show the list of names.  We'll
start again with the Xcode `Single View Application` template.  Xcode 6
and above will automatically create a `Main.storyboard` file and include
it in our build settings.  We will go ahead and remove this file and
update `Info.plist` file (under `Supporting Files`) to remove the
reference to it by clicking on the minus sign after selecting `Main
storyboard file base name`.

<a href="https://imgur.com/wTiqnWZ"><img src="https://i.imgur.com/wTiqnWZ.png" title="source: imgur.com" /></a>

Without the storyboard, we'll have to set up our window and root view
controller manually by changing the `didFinishLaunchingWithOptions`
method in our `AppDelegate`.  More on the `AppDelegate` can be found in
the [[basic application architecture guide|Application Architecture]].



```swift
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        window = UIWindow(frame: UIScreen.main.bounds)
        window?.rootViewController = ViewController()
        window?.makeKeyAndVisible()
        return true
    }
    ...
}
```
Notice that we made use of the main `ViewController` class that Xcode
generated for us.  Now we can add our table of names to this view
controller.  We do everything programmatically here, but we could have
just as easily set up a separate `.xib` file for this view controller.
If you are unfamiliar with table views please refer to
the [[table view guide|Table View Guide]].


```swift
import UIKit

class ViewController: UIViewController, UITableViewDataSource {
    let names = ["Brent Berg", "Cody Preston", "Kareem Dixon", "Xander Clark",
        "Francis Frederick", "Carson Hopkins", "Anthony Nguyen", "Dean Franklin",
        "Jeremy Davenport", "Rigel Bradford", "John Ball", "Zachery Norman",
        "Valentine Lindsey", "Slade Thornton", "Jelani Dickson", "Vance Hurley",
        "Wayne Ellison", "Kasimir Mueller", "Emery Pruitt", "Lucius Lawrence",
        "Kenneth Mendez"]

    var tableView: UITableView!

    override func viewDidLoad() {
        super.viewDidLoad()
        tableView = UITableView(frame: view.frame)
        tableView.dataSource = self
        tableView.register(UITableViewCell.self, forCellReuseIdentifier: "NameCell")
        view.addSubview(tableView)
    }

    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return names.count
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "NameCell", for: indexPath)
        cell.textLabel?.text = names[indexPath.row]
        return cell
    }
}
```

Running the app at this point gives us:

<a href="https://imgur.com/OuawCqi"><img src="https://i.imgur.com/OuawCqil.png" title="source: imgur.com" /></a>

#### Adding the navigation controller and setting its root view controller
Next we'll need to add a navigation controller and set its [_root view
controller_][rootvc]&mdash;the first view controller that will be shown
when the navigation controller is loaded&mdash;to be our main view
controller with the table.

We can do this all in code by simply instantiating a
`UINavigationController` and setting its properties.  Since we want the
navigation controller to be the first view controller shown when the app is
loaded, we can do this in our `AppDelegate`'s
`didFinishLaunchingWithOptions` method where we also set the
_application window_'s root view controller to be the navigation
controller.

```swift
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        window = UIWindow(frame: UIScreen.main.bounds)
        let mainVC = ViewController()
        let navigationVC = UINavigationController(rootViewController: mainVC)
        window?.rootViewController = navigationVC
        window?.makeKeyAndVisible()
        return true
    }
    ...
}
```

Running the application at this point will show the same table with a
grey navigation bar at the top.

<a href="https://imgur.com/5WtqwmM"><img src="https://i.imgur.com/5WtqwmMl.png" title="source: imgur.com" /></a>

#### Initializing and pushing a view controller onto the stack
As in our storyboard example, we'll need to create another view
controller class to display and allow the user to edit an individual
name.  Instead of laying out our view programmatically as we did in the
main `ViewController` class, we'll create new class `NameController` and
associated `.xib` by selecting `File -> New -> File... -> iOS -> Source
-> Cocoa Touch Class`.  Be sure to tick in the `Also Create XIB file`.

<a href="https://imgur.com/2LNtYpi"><img src="https://i.imgur.com/2LNtYpi.png" title="source: imgur.com" /></a>

_NB: Most of the time you'll want to be consistent and use only one
method of laying out your views (programmatic, separate `.xib`s, or have
your app entirely within storyboards).  Here we use both `.xib` and
programmatic layout to emphasize the point that anything outside a
storyboard cannot use segues and thus must programmatically manipulate
the navigation view controller._

We can now open up `NameController.xib` in Interface Builder. As before,
we'll add two text fields to this view controller to store the first and
last parts of the name the user will be editing. Using the Assistant
Editor (tuxedo) view we also create outlets in our `NameController`
class corresponding to the two text fields by controlling dragging the
fields into the code view.

<a href="https://imgur.com/bEH8mLO"><img src="https://i.imgur.com/bEH8mLO.gif" title="source: imgur.com" /></a>

We'll go ahead and add the code that will allow us to configure the
`NameController` to with the `fullName` that it will display when
loaded.

```swift
import UIKit

class NameController: UIViewController {

    @IBOutlet weak var lastNameTextField: UITextField!
    @IBOutlet weak var firstNameTextField: UITextField!

    var fullName: String?

    override func viewDidLoad() {
        if let fullName = self.fullName {
            let firstLast = fullName.components(separatedBy: " ")
            firstNameTextField.text = firstLast[0]
            lastNameTextField.text = firstLast[1]
        }
    }
}
```

_NB: The following only affects Swift projects:_

_Previously when initializing a view controller class named `Foo` via the
default (argumentless) initializer, UIKit will look for a matching
`Foo.xib` and load the UI elements from this file.  In the latest
versions of Xcode Apple has changed this behavior (for Swift classes
only) to look for an associated `.xib` named `YourProjectName.Foo.xib`._

_There are a few ways to work around this behavior discussed
[here][vcinit1] and [here][vcinit2].  In this example we simply
renamed our `NameController.xib` file to
`NavigationNoStoryboard.NameController.xib` where
`NavigationNoStoryboard` was the the name of our project._

[vcinit1]: http://stackoverflow.com/a/25152545
[vcinit2]: http://stackoverflow.com/a/25539016

Remember that we want to show the `NameController` and add it to the
navigation stack when the user selects a name in our main
`ViewController` containing the table of names.  By having our main
`ViewController` implement the `UITableViewDelegate` protocol we have a
convenient hook to respond to the row selection event by overriding the
[`didSelectRowAtIndexPath`][didselectrow] method.  Again, you should
refer to the [[table view guide|Table View Guide]] if you are unfamiliar with this aspect of
table views.

[didselectrow]: https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/occ/intfm/UITableViewDelegate/tableView:didSelectRowAtIndexPath:

```swift
class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
    ...

    override func viewDidLoad() {
        super.viewDidLoad()
        tableView = UITableView(frame: view.frame)
        tableView.dataSource = self
        tableView.delegate = self
        tableView.register(UITableViewCell.self, forCellReuseIdentifier: "NameCell")
        view.addSubview(tableView)
    }

    ...

    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        let nameVC = NameController()
        nameVC.fullName = names[indexPath.row]

        tableView.deselectRow(at: indexPath, animated: true)
        self.navigationController?.pushViewController(nameVC, animated: true)
    }
}
```

Notice that we are able to obtain a reference to the navigation
controller via `self.navigationController`.  This is set automatically
for us by UIKit when any view controller comes onto the navigation
stack.  In this case, it happened when we set this view controller as
our navigation controller's root view controller in the `AppDelegate`
above.

We push `nameVC` onto the navigation stack and display it as the top
view controller with `pushViewController`.  After this call is made, the
`nameVC.navigationController` property will also be automatically set to
the same navigation controller.

_NB: Technically in Swift, prefixing a property with `self.` is not
required and is specifically recommended against by both
[github's][githubstyleguide] and [Ray
Wenderlich's][raywenderlichstyleguide] style guides.  We include it here
to emphasize that this is a built-in property on the view controller._

[githubstyleguide]: https://github.com/github/swift-style-guide#only-explicitly-refer-to-self-when-required
[raywenderlichstyleguide]: https://github.com/raywenderlich/swift-style-guide#use-of-self

Running the application at this stage gives us the following behavior.

#### Passing information back up the stack with a delegate

We still need to implement the ability to save changes to a name in
`NameController` back to the table view in the main `ViewController`.
Since there's no such thing as an _unwind segue_ without a storyboard,
we'll have to implement our own mechanism for sending the new name back
to the main `ViewController`.

The typical pattern to accomplish this in iOS programmatically is to use a
[delegate][delegatepattern].  What this means in this case is that the
`NameController` needs to keep a reference to the main `ViewController`
so that it can inform the main `ViewController` when the user has saved
changes to a name and what the new name is.

[delegatepattern]: https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html

However, since we defined our `NameController` outside of a storyboard,
there is no way in general, to tell what view controller has
instantiated it and pushed it onto the navigation stack (if there is
even a navigation stack).  For example, later on, we might want to reuse
`NameController` to edit names a different situation where we
instantiated it and presented it from a view controller class we haven't
even created at this time.

Thus maintaining a reference to something of type `ViewController` would
unnecessarily couple the `ViewController` and `NameController` classes.
To solve this problem we can introduce a protocol (sometimes called
interface in other programming environments) `NameControllerDelegate` to
represent something that is able to respond events from
`NameController`.   We'll make our main `ViewController` implement
`NameControllerDelegate` similar to how it implemented
`UITableViewDelegate` in order to respond to events from the
`UITableView`.

Our `NameControllerDelegate` protocol defines a single method
`didSaveName` that will be called when the user has initiated a save
action in our `NameController`.  In order to call this method, we'll need
to keep a reference to the `delegate` in our `NameController`.

Lastly, we need to add a save button to our navigation bar and set its
action to call a method `saveButtonTapped` in our `NameController` that
will propagate the event to our `NameControllerDelegate`.

After putting everything together the code looks like this:

```swift
import UIKit

protocol NameControllerDelegate: class {
    func nameController(nameVC: NameController, didSaveName name: String)
}

class NameController: UIViewController {

    @IBOutlet weak var lastNameTextField: UITextField!
    @IBOutlet weak var firstNameTextField: UITextField!

    weak var delegate: NameControllerDelegate?
    var fullName: String?

    override func viewDidLoad() {
        self.navigationItem.rightBarButtonItem = UIBarButtonItem(title: "Save", style: .plain, target: self, action: #selector(NameController.saveButtonTapped)
        self.navigationItem.title = "Edit Name"

        if let fullName = self.fullName {
            let firstLast = fullName.components(separatedBy: " ")
            firstNameTextField.text = firstLast[0]
            lastNameTextField.text = firstLast[1]
        }
    }

    func saveButtonTapped() {
        let name = firstNameTextField.text + " " + lastNameTextField.text
        delegate?.nameController(self, didSaveName: name)
    }
}
```

```swift
import UIKit

class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate, NameControllerDelegate {
    var names = ["Brent Berg", "Cody Preston", "Kareem Dixon", "Xander Clark",
        "Francis Frederick", "Carson Hopkins", "Anthony Nguyen", "Dean Franklin",
        "Jeremy Davenport", "Rigel Bradford", "John Ball", "Zachery Norman",
        "Valentine Lindsey", "Slade Thornton", "Jelani Dickson", "Vance Hurley",
        "Wayne Ellison", "Kasimir Mueller", "Emery Pruitt", "Lucius Lawrence",
        "Kenneth Mendez"]

    var tableView: UITableView!
    var selectedIndexPath: IndexPath?

    ...

    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        let nameVC = NameController()
        nameVC.fullName = names[indexPath.row]
        nameVC.delegate = self

        selectedIndexPath = indexPath
        tableView.deselectRow(at: indexPath, animated: true)
        self.navigationController?.pushViewController(nameVC, animated: true)
    }

    func nameController(nameVC: NameController, didSaveName name: String) {
        if let indexPath = selectedIndexPath {
            names[indexPath.row] = name
            tableView.reloadRows(at: [indexPath], with: .automatic)
        }
        self.navigationController?.popViewControllerAnimated(true)
    }
}
```

<a href="https://imgur.com/t1krDPG"><img src="https://i.imgur.com/t1krDPG.gif" title="source: imgur.com" /></a>

There are quite a few things to note in the above code.

* We restrict that only class types (as opposed to structs) can
  implement our `NameControllerDelegate` protocol by including the
`class` keyword in the declaration in the inherited protocols list after
the colon.  This is so we can declare our `delegate` property as a
`weak` reference (since structs cannot be weak references).  This point
is language specific to Swift.

* The reason we need our `delegate` property to be a `weak` reference is
  due to the fact that iOS manages (and frees) memory by using a
technique called [automatic reference counting][arc1] (ARC).  Since
delegates commonly refer back to objects that instantiated them (or
objects referred to by these objects), **we'll always want to declare
our delegate properties as `weak`** in order to avoid strong reference
cycles that lead to memory leaks.  An extensive discussion of ARC can be
found [here][arc2].

* The `navigationItem` property of `NameController` is used by the
  navigation controller to determine what to display in the
navigation bar when `NameController` is on top of the navigation stack.
This provides us with a convenient hook to customize the content of the
navigation bar.  More on this can be found below in discussion of
customizing the navigation bar's appearance
[below](#customizing-the-appearance-of-navigation-bar).

* When creating the `Save` button by instantiating a `UIBarButtonItem`
  we specified how to respond to the button being tapped with the
`target` and `action` parameters.  This is an example of [target-action
pattern][targetaction] (sometimes called target-selector).  This
invocation basically means "call the method identified by `action` on
the `target` object when the button is tapped".

_NB:  In Objective-C the `action` parameter has to a be
[`selector`][selector].  In Swift, strings are automatically converted
to selectors when necessary._

* As before in our storyboard example, we need to maintain an additional
  state variable `selectedIndexPath` so that when the delegate method
`didSaveName` is called, we can know which name to update.

* Notice that we set `nameVC.delegate = self` when configuring the
  `NameController` in `didSelectRowAtIndexPath` since we implement
`NameControllerDelegate` in `ViewController`.  It is a common mistake
to forget to set the delegate.

* We deselect the row in `didSelectRowAtIndexPath` since we won't want
  it to be selected once we go back from the `NameController`.

* We follow delegate method [naming conventions][namingconventions] by
  passing the delegating object (`NameController`) back as the first
argument in our `didSaveName` method.

* Without an _unwind segue_ we have to manually pop top the view
  controller in `didSaveName`.  When this method is called, this will be
the `NameController`.  Notice that we didn't have to set up this action
for the `Back` button due to a built-in behavior.

[arc1]: http://en.wikipedia.org/wiki/Automatic_Reference_Counting
[arc2]: https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/AutomaticReferenceCounting.html
[targetaction]: https://developer.apple.com/library/ios/documentation/General/Conceptual/Devpedia-CocoaApp/TargetAction.html
[selector]: https://developer.apple.com/library/mac/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html
[namingconventions]: https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-1001803-BCIDAIJE

## Customizing the appearance of navigation bar
There are several ways to customize the appearance of the navigation bar
and control what items appear in it.

### Understanding navigation bars and navigation items
In order to know which object's properties we need to change to get the
desired appearance, we first need to understand a bit about how
navigation bars and navigation items work.

Each `UINavigationController` has a [`navigationBar`][navbarproperty] of
type [`UINavigationBar`][uinavigationbar].  Each `UIViewController` has
an associated [`navigationItem`][navitemproperty] of type
[`UINavigationItem`][uinavigationitem].

The navigation bar associated with a navigation controller maintains a
[stack of `UINavigationItems`][navitems] that is parallel to the
navigation stack of view controllers.  When a view controller is pushed
onto the navigation stack its `navigationItem` gets pushed onto the
navigation bar's stack of navigation items.

The [top][topitem] and [second from top][backitem] `UINavigationItems`
in the navigation bar's item stack are used to determine which
components are added to navigation bar.  Specifically the top navigation
item determines the title view and button(s) on the right hand side of
the nav bar.  It can also determine the button(s) on the left hand side
of the nav bar, but if no left-side button is set, the navigation bar
will use the _second from top navigation item_ in order to determine
what "Back" button to show.

In some rarer cases you may want to use a `UINavigationBar` without the
backing of a `UINavigationController`.  In these instances, you may have
to manually manage the `UINavigationBar`'s navigation item stack by
[pushing][pushnavitem]/[popping][popnavitem] navigation items in order
to achieve the appearance you want.

[navitems]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationBar_Class/index.html#//apple_ref/occ/instp/UINavigationBar/items
[topitem]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationBar_Class/index.html#//apple_ref/occ/instp/UINavigationBar/topItem
[backitem]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationBar_Class/index.html#//apple_ref/occ/instp/UINavigationBar/backItem
[navbarproperty]: https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UINavigationController_Class/index.html#//apple_ref/occ/instp/UINavigationController/navigationBar
[navitemproperty]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instp/UIViewController/navigationItem
[uinavigationbar]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationBar_Class/index.html
[uinavigationitem]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationItem_Class/index.html
[pushnavitem]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationBar_Class/index.html#//apple_ref/occ/instm/UINavigationBar/pushNavigationItem:animated:
[popnavitem]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationBar_Class/index.html#//apple_ref/occ/instm/UINavigationBar/popNavigationItemAnimated:

### Customization inside a storyboard
When working in a project with storyboards, many of the things you might
want to change about a navigation bar's appearance can be edited in the
Attributes Inspector by selecting the navigation bar associated with
your navigation controller.

<a href="https://imgur.com/judIATc"><img src="https://i.imgur.com/judIATc.png" title="source: imgur.com" /></a>

The navigation item with your view controller can also be edited
directly in the storyboard.  For example, to add more buttons to the
navigation item, you can simply drag buttons from the Object Library as
we did in our storyboard example
[above](#unwind-segues-and-passing-information-back-up-the-hierarchy).
Note that a view controller's the navigation item will not be an element
in your storyboard by default.  You must drag a navigation item from the
Object Library to your view controller (as we did in our storyboard
example above) in order to have access to it&mdash;although, embedding
your view controller in a navigation controller will automatically add
the navigation item.


### Navigation bar colors, background image, and text attributes
There are a [few properties][navbarproperites] that you can manipulate in
order to control the appearance of a navigation bar.  Since a navigation
controller is associated with a single navigation bar, __any change here
will be visible across all view controllers in a navigation controller's
stack.__  Apple has been changing the behavior of these properties
somewhat frequently in recent iOS versions, so you may have to
experiment a bit to achieve the effect you want.

[navbarproperites]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationBar_Class/index.html#//apple_ref/doc/uid/TP40006887-CH3-SW16

Continuing with our example from above we can customize our navigation
bar as follows.

```swift
class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate, NameControllerDelegate {
    ...

    override func viewDidLoad() {
        ...

        self.navigationItem.title = "Names"
        if let navigationBar = navigationController?.navigationBar {
            navigationBar.setBackgroundImage(UIImage(named: "codepath-logo"), forBarMetrics: .default)
            navigationBar.tintColor = UIColor(red: 1.0, green: 0.25, blue: 0.25, alpha: 0.8)

            let shadow = NSShadow()
            shadow.shadowColor = UIColor.gray.colorWithAlphaComponent(0.5)
            shadow.shadowOffset = CGSizeMake(width: 2, high: 2)
            shadow.shadowBlurRadius = 4
            navigationBar.titleTextAttributes = [
                NSFontAttributeName : UIFont.boldSystemFont(ofSize: 22),
                NSForegroundColorAttributeName : UIColor(red: 0.5, green: 0.15, blue: 0.15, alpha: 0.8),
                NSShadowAttributeName : shadow
            ]
        }
    }
```

```objc
@implementation DetailViewController

- (void)viewDidLoad {

    self.navigationItem.title = @"Names";
    UINavigationBar *navigationBar = self.navigationController.navigationBar;
    [navigationBar setBackgroundImage:[UIImage imageNamed:@"codepath-logo"] forBarMetrics:UIBarMetricsDefault];
    navigationBar.tintColor = [UIColor colorWithRed:1.0 green:0.25 blue:0.25 alpha:0.8];
    
    NSShadow *shadow = [NSShadow new];
    shadow.shadowColor = [[UIColor grayColor] colorWithAlphaComponent:0.5];
    shadow.shadowOffset = CGSizeMake(2, 2);
    shadow.shadowBlurRadius = 4;
    navigationBar.titleTextAttributes = @{NSFontAttributeName : [UIFont boldSystemFontOfSize:22],
                                          NSForegroundColorAttributeName : [UIColor colorWithRed:0.5 green:0.15 blue:0.15 alpha:0.8],
                                          NSShadowAttributeName : shadow};

}
```

<a href="https://imgur.com/AJjy8Nq"><img src="https://i.imgur.com/AJjy8Nq.gif" title="source: imgur.com" /></a>

### Title text and view of a navigation item

Notice that above we were able to set the navigation bar's title text by
setting `navigationItem.title` on our _view controller_'s navigation
item.  Since the navigation item is distinct per view controller, we can
set separate titles for each view controller.

In rare cases you may wish to replace a navigation item's entire
[`titleView`][titleview].  This is useful for example if you need
customized styling of the title text only for a specific view
controller.  Continuing with our previous example

```swift
class NameController: UIViewController {
    ...

    override func viewDidLoad() {
        navigationItem.rightBarButtonItem = UIBarButtonItem(title: "Save", style: .plain, target: self, action: #selector(NameController.saveButtonTapped)

        let titleLabel = UILabel()

        let shadow = NSShadow()
        shadow.shadowColor = UIColor.redColor().colorWithAlphaComponent(0.5)
        shadow.shadowOffset = CGSizeMake(width: 2, high: 2)
        shadow.shadowBlurRadius = 4

        let titleText = NSAttributedString(string: "Edit Name", attributes: [
            NSFontAttributeName : UIFont.boldSystemFont(ofSize: 28),
            NSForegroundColorAttributeName : UIColor(red: 0.5, green: 0.25, blue: 0.15, alpha: 0.8),
            NSShadowAttributeName : shadow
            ])

        titleLabel.attributedText = titleText
        titleLabel.sizeToFit()
        navigationItem.titleView = titleLabel

        ...
    }

}
```

<a href="https://imgur.com/BYV6p9s"><img src="https://i.imgur.com/BYV6p9s.gif" title="source: imgur.com" /></a>

[titleview]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationItem_Class/index.html#//apple_ref/occ/instp/UINavigationItem/titleView

### Setting bar button items for a navigation item
You can also configure the button(s) that appear to the
[left][leftbutton] and [right][rightbutton] of the title in a navigation
item by providing your own `UIBarButtonItem` or array of
`UIBarButtonItems`.  The [`UIBarButtonItem`][uibarbuttonitem] also has a
number of properties you can use to customize it.  Of particular note is
the fact that you can provide an arbitrary `UIView` as a custom view.

[leftbutton]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationItem_Class/index.html
[rightbutton]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationItem_Class/index.html#//apple_ref/occ/instp/UINavigationItem/rightBarButtonItems
[uibarbuttonitem]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIBarButtonItem_Class/index.html

```swift
class NameController: UIViewController {
    ...

    override func viewDidLoad() {
        let saveButton = UIBarButtonItem(title: "Save", style: .plain, target: self, action: #selector(NameController.saveButtonTapped)

        let segmentedControl = UISegmentedControl(items: ["Foo", "Bar"])
        segmentedControl.sizeToFit()
        let segmentedButton = UIBarButtonItem(customView: segmentedControl)

        let dummyButton = UIBarButtonItem(title: "Dummy", style: .plain, target: nil, action: nil)

        navigationItem.rightBarButtonItems = [saveButton, segmentedButton]
        navigationItem.leftBarButtonItem = dummyButton
        ...
   }
   ...
}
```
<a href="https://imgur.com/FptSf6y"><img src="https://i.imgur.com/FptSf6yl.png" title="source: imgur.com" /></a>

#### The Back button
Notice that above when we set the top navigation item's
`leftBarButtonItem`, the "Back" button no longer appeared.  In order to
customize the back button we have the `leftBarButtonItem` and
`leftBarButtonItems` properties set to `nil` for top navigation item.
We must manipulate the navigation item that is _second from the top_ in
the stack.  This will generally be the navigation item associated with
the view controller that just pushed the current view controller on top
of the stack.

By default the navigation bar will use the second from top navigation
item's title text as the back button text for the top view controller.
We can customize this, for example, by having the back button say "Cancel"
instead.

```swift
class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate, NameControllerDelegate {
     ...
     override func viewDidLoad() {
        ...

        self.navigationItem.title = "Names"
        self.navigationItem.backBarButtonItem = UIBarButtonItem(title: "Cancel", style: .plain, target: nil, action: nil)
        ...
    }

    func tableView(tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        let nameVC = NameController()
        nameVC.fullName = names[indexPath.row]
        nameVC.delegate = self

        selectedIndexPath = indexPath
        tableView.deselectRowAtIndexPath(indexPath, animated: true)
        self.navigationController?.pushViewController(nameVC, animated: true)

    }
    ...
}
```

<a href="https://imgur.com/W4XwDwF"><img src="https://i.imgur.com/W4XwDwF.gif" title="source: imgur.com" /></a>

<!-- TODO: ## Example: implementing a search bar in the navigation bar -->
