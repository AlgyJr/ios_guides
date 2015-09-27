<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
- [Overview](#overview)
- [Static Forms](#static-forms)
  - [Example: Basic preferences page](#example-basic-preferences-page)
- [Dynamic Forms](#dynamic-forms)
  - [Challenges of tables with dynamically created form inputs](#challenges-of-tables-with-dynamically-created-form-inputs)
  - [Example: Basic preferences page revisited](#example-basic-preferences-page-revisited)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Overview

This guide covers how to handle basic form input in iOS.

Unlike [HTML forms][formaction], in iOS there is no standard [form
submission action][formaction].  This means that it is up to you to
define _when_ a form's contents should be processed.  It also means that
you are responsible for _how_ to translate a form's state into
meaningful information for the the application's
[models](Application-Architecture#models), and vice versa.

[htmlforms]: http://www.w3.org/TR/html4/interact/forms.html
[formaction]: http://www.w3.org/TR/html4/interact/forms.html#adef-action

We'll focus on the very common use case of embedding form inputs in a
table view.  This will allow us to highlight some points that require
extra care when working with inputs in iOS.  For example, if the table
view uses [reusable cells](Table-View-Guide#reusing-uitableviewcells), a
cell containing a form input may be removed from memory as soon as it
disappears from the screen when the user scrolls the table.  This means
that you cannot necessarily rely on the state of the form itself to
store information the user has input.  We'll cover how to overcome this and
other problems below.

Here is a typical example of form inputs in a table view:

<a href="http://imgur.com/e4vUMW4"><img src="http://i.imgur.com/e4vUMW4.png" title="source: imgur.com" /></a>

## Static Forms
One way to avoid some complexity when working with form input in a table
view is to use [static cells][staticcells].  However, this is only
applicable when using Interface Builder and when your table view is
backed by a
[UITableViewController](Table-View-Guide#uitableview-vs-uitableviewcontroller).
Additionally you should only use static cells when the structure of your
forms will never change.

[staticcells]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/TableView_iPhone/TableViewCells/TableViewCells.html#//apple_ref/doc/uid/TP40007451-CH7-SW20

To create a table with static cells you'll need to drag a `Table View
Controller` into the scene from the Object Library and set `Content` to
`Static Cells` in the Attributes Inspector.

<a href="http://imgur.com/dtxyIoX"><img src="http://i.imgur.com/dtxyIoX.png" title="source: imgur.com" /></a>

You can now control the number of sections in your table and the the
number of cells by selecting the either "Table View" or "Section" in the
Outline view and using the Attributes Inspector.  Using Interface
Builder you can now design each individual cell as it would appear in
your app.  You can also connect the controls in your cells to
`@IBOutlets` and `@IBActions` in your UITableViewController's
corresponding subclass.  Since there is no danger of static cells being
removed from memory when they scroll off screen, you can choose whenever
you want to process the information from any form inputs into useful
information for the rest of your app.

### Example: Basic preferences page
Here is how we might implement a simple preferences page for an app.  To
demonstrate the fact that the preferences will be used in the rest of
the app we have a main `ViewController` that initializes and displays
the preferences.  It has an "Edit Preferences" button that will open up
our `PreferencesTableViewController`.

_NB: In practice, you might instead opt to use the [iOS Settings
Bundle][settingsbundle] feature when creating a preferences page._

[settingsbundle]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/UserDefaults/Preferences/Preferences.html#//apple_ref/doc/uid/10000059i-CH6-SW5

<a href="http://imgur.com/jt3Pmf6"><img src="http://i.imgur.com/jt3Pmf6.gif" title="source: imgur.com" /></a>

The preferences table has three _static_ cells each containing a single
[switch][uiswitch] corresponding to an on/off preference.  The swiches
are connected to `@IBOutlets` in our `PreferencesTableViewController`.
The preferences table view controller also has "Save" and "Cancel"
buttons connected to unwind segues in our main `ViewController`.

[uiswitch]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISwitch_Class/index.html

<a href="http://imgur.com/rF4Ww3W"><img src="http://i.imgur.com/rF4Ww3W.png" title="source: imgur.com" /></a>

The `Preferences` class provides us with a model to store the
preferences.

```swift
class Preferences {
   var autoRefresh = true, playSounds = true, showPhotos = true
}
```

In our preferences table view controller we initialize the state of the
switches to match the current preferences&mdash;which will be set by our
main view controller.

Notice that when a switch is toggled, we do not mutate the current
preferences since we might still cancel our changes.  Instead we provide
a way to create a new `Preferences` object from the current state of the
switches in the UI with the `preferencesFromTableData` method.  We'll use this
method in the unwind segue associated with the "Save" button.

We can wait to convert the state of the switches (as they appear in the
UI) to a `Preferences` object until we are ready to save because we are
using _static cells_.  This means that the cells do not get allocated
and removed from memory dynamically and that we can obtain `@IBOutlets`
to controls inside each specific cell.

```swift
import UIKit

class PreferencesTableViewController: UITableViewController {

    @IBOutlet weak var autoRefreshSwitch: UISwitch!
    @IBOutlet weak var soundsSwitch: UISwitch!
    @IBOutlet weak var showPhotosSwitch: UISwitch!

    // should be set by the class that instantiates this view controller
    var currentPrefs: Preferences!

    override func viewDidLoad() {
        super.viewDidLoad()
        currentPrefs = currentPrefs ?? Preferences()
        initSwitches()
    }

    private func initSwitches() {
        autoRefreshSwitch?.on = currentPrefs.autoRefresh
        soundsSwitch?.on = currentPrefs.playSounds
        showPhotosSwitch?.on = currentPrefs.showPhotos
    }

    func preferencesFromTableData() -> Preferences {
        var newPrefs = Preferences()
        newPrefs.autoRefresh = autoRefreshSwitch.on
        newPrefs.playSounds = soundsSwitch.on
        newPrefs.showPhotos = showPhotosSwitch.on
        return newPrefs
    }
}

```

In our main `ViewController` we set up the
`PreferencesTableViewController` with the current preferences before our
segue.  When coming back from the segue via the "Save" button we read
off the new preferences from the switches and update our `preferences`
object.  We don't take any action if the edit was "Canceled" because the
`PreferencesTableViewController` does not modify the `preferences`
object we pass in.

```swift
class ViewController: UIViewController {

    @IBOutlet weak var autoRefreshLabel: UILabel!
    @IBOutlet weak var playSoundsLabel: UILabel!
    @IBOutlet weak var showPhotosLabel: UILabel!

    var preferences: Preferences = Preferences() {
        didSet {
            updateLabels()
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        updateLabels()
    }

    private func updateLabels() {
        autoRefreshLabel.text = preferences.autoRefresh ? "Yes" : "No"
        playSoundsLabel.text = preferences.playSounds ? "Yes" : "No"
        showPhotosLabel.text = preferences.showPhotos ? "Yes" : "No"
    }

    override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
        if segue.identifier == "showPreferencesSegue" {
            // we wrapped our PreferencesTableViewController inside a UINavigationController
            let navController = segue.destinationViewController as UINavigationController
            let prefsVC = navController.topViewController as PreferencesTableViewController
            prefsVC.currentPrefs = self.preferences
        }
    }

    @IBAction func didSavePreferences(segue: UIStoryboardSegue) {
        if let prefsVC = segue.sourceViewController as? PreferencesTableViewController {
            self.preferences = prefsVC.preferencesFromTableData()
        }
    }

    @IBAction func didCancelPreferences(segue: UIStoryboardSegue) {
        // do nothing
    }
}
```

## Dynamic Forms
Static cells are quite limited because single `UITableView` has to
contain either _all static cells_ or have _all_ of its content be
provided dynamically.  For example it would not be possible to use
static cells if the number of rows in _any_ section of your table was
based on data loaded at runtime.

Furthermore, you can only use static cells in Interface Builder and when
your table view is backed by `UITableViewController`.

### Challenges of tables with dynamically created form inputs
There are a few additional challenges to overcome when working with form
inputs in a table with dynamic content:

1.  Since the cells are dynamically allocated, _inside your view
    controller_ there is no way to obtain `@IBOutlets` to an form input
inside a specific cell.  You must arrange a way for the cells to read
the state of its own form inputs and for the view controller to get this
information from the cell.

2. Likewise, if your view controller needs to respond to events from an
   individual cell, you must arrange a way for the cell to propagate the
event to the view controller.  You cannot simply associate an
`@IBAction` inside the view controller with an element inside a specific
cell.

3. Generally you'll be getting reusable cells to configure by calling
   `dequeueReusableCellWithIdentifier`.  This means you cannot rely on
the information contained in the form inputs in an individual cell to
persist when it scrolls off screen.  You will have to maintain all the
information necessary to populate the state of all form inputs in each
cell somewhere else.

4. You'll also have to update this information immediately if the user
interacts with the cell because, again, the form input itself cannot be
used to to store information once the cell is no longer on the screen.

5. If your form is used for editing data and provides a "Cancel" option,
   you must maintain an additional copy of the data seperate from the
initial data and seperate from the current value inside the form inputs.


### Example: Basic preferences page revisited
To see how these factors come into play, we'll reimplement our basic
preferences page example from [above](#example-basic-preferences-page)
to use dynamically created cells based on [prototype
cells](Table-View-Guide#using-prototype-cells) instead of static cells.

As before we have a main `ViewController` displays the current
preferences and has an "Edit Preferences" button that does a modal segue
to our `PreferencesViewController`.  The `ViewController` class also
contains `@IBActions` for unwind segues that happen when the user either
saves or cancels editing preferences.

<a href="http://imgur.com/8nbwUnu"><img src="http://i.imgur.com/8nbwUnu.png" title="source: imgur.com" /></a>

We renamed our `PreferencesTableViewController` to
`PreferencesViewController`.  It no longer has to be a subclass of
`UITableViewController`, and instead it only implements the
`UITableViewDataSource` protocol.  Other than replacing this type name,
the code in our `ViewController` class remains the same as above.  The
code for our `Preferences` model has not changed from above at all.

The bulk of our changes are in `PreferencesViewController`.  We need to
maintain the temporary state of our table as we are editing our
preferences outside of the cells themselves.  We introduce two
properties `tableStructure` and `prefValues` too keep track of this
state.

We write logic to convert a `Preferences` object into our `prefValues`.
This allow us to update the `prefValues` to store the state of table as
we are editing without modifying the original `Preferences` object that
is passed in.  Likewise we have a procedure obtain a new `Preferences`
object from the `prefValues`.  This allows the rest of the application
to obtain an updated `Preferences` once the user hits "Save".

```swift
enum PrefRowIdentifier : String {
    case AutoRefresh = "Auto Refresh"
    case PlaySounds = "Play Sounds"
    case ShowPhotos = "Show Photos"
}

class PreferencesViewController: UIViewController, UITableViewDataSource, PreferenceSwitchCellDelegate {
    @IBOutlet weak var tableView: UITableView!
    let tableStructure: [[PrefRowIdentifier]] = [[.AutoRefresh], [.PlaySounds, .ShowPhotos]]
    var prefValues: [PrefRowIdentifier: Bool] = [:]

    // should be set by the class that instantiates this view controller
    var currentPrefs: Preferences! {
        didSet {
            prefValues[.AutoRefresh] = currentPrefs.autoRefresh
            prefValues[.PlaySounds] = currentPrefs.playSounds
            prefValues[.ShowPhotos] = currentPrefs.showPhotos
            tableView?.reloadData()
        }
    }

    func preferencesFromTableData() -> Preferences {
        let ret = Preferences()
        ret.autoRefresh = prefValues[.AutoRefresh] ?? ret.autoRefresh
        ret.playSounds = prefValues[.PlaySounds] ?? ret.autoRefresh
        ret.showPhotos = prefValues[.ShowPhotos] ?? ret.autoRefresh
        return ret
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        currentPrefs = currentPrefs ?? Preferences()
        tableView.dataSource = self
    }

    func numberOfSectionsInTableView(tableView: UITableView) -> Int {
        return tableStructure.count
    }

    func tableView(tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
        return " "
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return tableStructure[section].count
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCellWithIdentifier("PreferenceSwitchCell") as PreferenceSwitchCell
        let prefIdentifier = tableStructure[indexPath.section][indexPath.row]
        cell.prefRowIdentifier = prefIdentifier
        cell.onOffSwitch.on = prefValues[prefIdentifier]!
        cell.delegate = self
        return cell
    }

    func preferenceSwitchCellDidToggle(cell: PreferenceSwitchCell, newValue: Bool) {
        prefValues[cell.prefRowIdentifier] = newValue
    }
}

```

<!--- TODO link to delegate pattern -->

Finally we need a way for our custom cell containing a single switch to
know which preference it represents and to notify our view controller
immediately once its switch is toggled.  We do this by implementing a
custom `PreferenceSwitchCell` class with a corresponding delegate
`PreferenceSwitchCellDelegate` that is implemented by our
`PreferencesViewController`.

The action of the switch in our protoype cell is connected to our code
in `PreferenceSwitchCell` by an `@IBAction`.  We notify our view
controller once this event is triggered by propagating the change
through the delegate.

```swift
protocol PreferenceSwitchCellDelegate: class {
    func preferenceSwitchCellDidToggle(cell: PreferenceSwitchCell, newValue:Bool)
}

class PreferenceSwitchCell: UITableViewCell {
    @IBOutlet weak var descriptionLabel: UILabel!
    @IBOutlet weak var onOffSwitch: UISwitch!

    weak var delegate: PreferenceSwitchCellDelegate?

    var prefRowIdentifier: PrefRowIdentifier! {
        didSet {
            descriptionLabel?.text = prefRowIdentifier?.rawValue
        }
    }

    @IBAction func didToggleSwitch(sender: AnyObject) {
        delegate?.preferenceSwitchCellDidToggle(self, newValue: onOffSwitch.on)
    }
}

```

<!---
### Example: Multi-Select with Dynamic Content

### Example: Drop-Down Select
-->

