<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Overview](#overview)
- [Your first `UITableView`](#your-first-uitableview)
- [Reusing `UITableViewCells`](#reusing-uitableviewcells)
    - [Notes about the cell reuse pattern:](#notes-about-the-cell-reuse-pattern)
- [Custom cells](#custom-cells)
  - [Using prototype cells](#using-prototype-cells)
  - [Creating a separate NIB for your cell](#creating-a-separate-nib-for-your-cell)
  - [Laying out your cell programmatically](#laying-out-your-cell-programmatically)
- [Variable height cells](#variable-height-cells)
- [References](#references)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

In depth guide for using UITableView

## Overview

[`UITableViews`][uitableview] are one of the most commonly used views in iOS
programming.  They are used for displaying grouped lists of cells.  Here are
some examples of `UITableViews`:

![Contacts App](http://i.imgur.com/r7kRtLMl.png)
<!--- TODO: Add some sample images of UITableViews -->

`UITableViews` can be highly performant, displaying thousands of rows (*cells*) of
data. They also have common behavior built-in such as scrolling, "editing mode", and
the ability to animate the addition or removal of rows.

This guide will cover the fundamentals of using tableviews.

[uitableview]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html

## Your first `UITableView`

As with other views in the Cocoa Touch framework, in order to use a
`UITableView` you provide it with [delegates][delegatepattern] that are able to answer questions
about what to show in the table and how the application should respond to
certain user interactions with the table.

The `UITableView` has two delegates that you must provide by setting the
corresponding properties on your `UITableView` object.

1. The [`dataSource`][datasource] property must be set to an object that
implements the [`UITableViewDataSource`][uitableviewdatasource] protocol.
This object is responsible for the content of the table including providing the
actual [`UITableViewCells`][uitableviewcell] that will be shown.

2. The [`delegate`][delegate] property must be set to an object that implements
the [`UITableViewDelegate`][uitableviewdelegate] protocol.  This object
controls the basic visual appearance of and user interactions with the table.
It is not technically mandatory for you provide your own `delegate`, but in
practice you will almost always want to do something that requires implementing
your own `UITableViewDelegate`.

[delegatepattern]: http://en.wikipedia.org/wiki/Delegation_pattern
[datasource]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instp/UITableView/dataSource
[uitableviewdatasource]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html
[uitableviewcell]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/
[delegate]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instp/UITableView/delegate
[uitableviewdelegate]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html

The following is the most basic way to set up a UITableView.

```swift
import UIKit

class ViewController: UIViewController, UITableViewDataSource {
    @IBOutlet weak var myFirstTableView: UITableView!

    let data = ["New York, NY", "Los Angeles, CA", "Chicago, IL", "Houston, TX",
        "Philadelphia, PA", "Phoenix, AZ", "San Diego, CA", "San Antonio, TX",
        "Dallas, TX", "Detroit, MI", "San Jose, CA", "Indianapolis, IN",
        "Jacksonville, FL", "San Francisco, CA", "Columbus, OH", "Austin, TX",
        "Memphis, TN", "Baltimore, MD", "Charlotte, ND", "Fort Worth, TX"]

    override func viewDidLoad() {
        super.viewDidLoad()
        myFirstTableView.dataSource = self
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = UITableViewCell(style: .Default, reuseIdentifier: nil)
        cell.textLabel?.text = data[indexPath.indexAtPosition(1)]
        return cell
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return data.count
    }
}
```

Provided that the `@IBOutlet` `myFirstTableView` has been connected to a
`UITableView` in your storyboard, you will see something like this when running
the above code:

<!--- TODO: Link to documentation showing how to connect to IBOutlets -->
![Cities Table](http://i.imgur.com/ZOakSLDl.png)

__Notice that we set `self.myFirstTableView.dataSource = self`__ in the
`viewDidLoad` method.  A common error that will result in a blank or misbehaving
table is forgetting to set the `dataSource` or `delegate` property on your
`UITableView`.

In this case, since the only view managed by our `ViewController` is the table, we
also have our `ViewController` implement `UITableViewDataSource` so that all the
code for this screen is in one place.  This is a fairly common pattern when
using Cocoa Touch delegates, but you may want to create a separate class to
implement `UITableViewDataSource` or `UITableViewDelegate` in more complex
situations.

We implement the two required methods in the `UITableViewDataSource` protocol:

1. `func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int`
is responsible for telling the `UITableView` how many rows are in each section
of the table.  Since we only have one section, we simply return the length of
our `data` array which corresponds to the number of total cells we want.  To
create tables with multiple sections we would implement the
[`numberOfSections`][numberofsections] method and possibly return different
values in our [`numberOfRowsInSection`][numberofrowsinsection] method depending
the `section` that was passed in.

2. `func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell`
is responsible for returning a preconfigured cell that will be used to render
the row in the table specified by the `indexPath`.   The `indexPath` is an
[`NSIndexPath`][nsindexpath] of length 2.  It contains two integers:
`indexPath.indexAtPosition(0)` is the section number, and
`indexPath.indexAtPosition(1)` is the row number in the given section.

[numberofsections]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instm/UITableView/numberOfSections
[numberofrowsinsection]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instm/UITableView/numberOfRowsInSection:
[nsindexpath]: https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSIndexPath_Class/index.html

## Reusing `UITableViewCells`

The [`cellForRowAtIndexPath`][cellforrowatindexpath] method has to return an
instance of `UITableViewCell` that is configured with the data for the row
specified by the `indexPath`.  In the above code we created a new instance of
the Cocoa Touch-provided `UITableViewCell` class for each call to
`cellForRowAtIndexPath`.  Since our table had only a few simple cells you might
not have noticed any appreciable performance drop.  However, in practice, you
will almost __never create a new cell object for each row__ due to performance
costs and memory implications.  This becomes especially important once you start
creating more complex cells or have tables with large numbers of rows.

In order to avoid the expensive costs of creating a new cell object for each
row, we can adopt a strategy of *cell reuse*.  Notice that the table can only
display a small number of rows on the screen at once.  This means we only have
to create *at most* as many `UITableViewCell` objects as there are rows that
appear on the screen at once.  Once a row disappears from view&mdash;say when
the user scrolls the table&mdash;we can reuse the same cell object to render
another row that comes into view.

To implement such a strategy from scratch we would need to know which rows are
currently being displayed and to be able to respond if the set of visible rows
is changed.  Luckily `UITableView` has built-in methods that make cell reuse
quite simple to implement.  We can modify our code example above to read

[cellforrowatindexpath]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/occ/intfm/UITableViewDataSource/tableView:cellForRowAtIndexPath:

```swift
import UIKit

class ViewController: UIViewController, UITableViewDataSource {
    @IBOutlet weak var myFirstTableView: UITableView!

    let kCellIdentifier = "com.codepath.MyFirstTableViewCell"

    let data = ["New York, NY", "Los Angeles, CA", "Chicago, IL", "Houston, TX",
        "Philadelphia, PA", "Phoenix, AZ", "San Diego, CA", "San Antonio, TX",
        "Dallas, TX", "Detroit, MI", "San Jose, CA", "Indianapolis, IN",
        "Jacksonville, FL", "San Francisco, CA", "Columbus, OH", "Austin, TX",
        "Memphis, TN", "Baltimore, MD", "Charlotte, ND", "Fort Worth, TX"]

    override func viewDidLoad() {
        super.viewDidLoad()
        myFirstTableView.dataSource = self
        myFirstTableView.registerClass(UITableViewCell.self, forCellReuseIdentifier: kCellIdentifier)
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCellWithIdentifier(kCellIdentifier, forIndexPath: indexPath) as UITableViewCell
        cell.textLabel?.text = data[indexPath.indexAtPosition(1)]
        return cell
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return data.count
    }
}
```

In `viewDidLoad` we call our `UITableView`'s
[`registerClass:forCellReuseIdentifier:`][registerclass] to associate the
built-in class `UITableViewCell` with the constant string identifier
`kCellIdentifier`.  Notice that we do not explicitly create an instance here.
The `UITableView` will handle the creation of all cell objects for us.

In `cellForRowAtIndexPath`, we call
[`dequeueReusableCellWithIdentifier:forIndexPath:`][dequeuecell] to obtain a
pre-created instance of `UITableViewCell` and then we proceed to populate this
cell with the data for the given row before returning it.

[registerclass]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instm/UITableView/registerClass:forCellReuseIdentifier:
[dequeuecell]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instm/UITableView/dequeueReusableCellWithIdentifier:forIndexPath:

#### Notes about the cell reuse pattern
* Be sure to provide a unique reuse identifier for each type of cell that you
  in your application so that you don't end up accidentally getting an instance
of the wrong type of cell.

* When we explicitly instantiated each cell object in `cellForRowAtIndexPath` we
  were able to specify the cell `style: .Default`.  When we called
`dequeueReusableCellWithIdentifier` there was no place to specify the style.
When using `dequeueReusableCellWithIdentifier` you have no over the
initilization of your cell.  [In practice][cellstyle], you will want to create
your own subclass of `UITableViewCell` and add initialization common to all
cells in the class in the initializer.

* Any customization of the cell on a per row basis should be done in
`cellForRowAtIndexPath`.  When designing a custom cell class be sure to allow
access to the properties you need to change on a per row basis.  In this case
the built-in `UITableViewCell` gives us access to its `textLabel` so that we
are able to set different text for each row.

* There are no guarantees on the state of the cell that is returned by
`dequeueReusableCellWithIdentifier`.  *The cell will not necessarily be in the
newly initialized state.*  In general, it will have properties that were
previously set when configuring it with the data of another row.  Be sure
reconfigure *all* properties to match the data of the current row!


[cellstyle]: http://stackoverflow.com/questions/13174972/setting-style-of-uitableviewcell-when-using-ios-6-uitableview-dequeuereusablecel

## Custom cells

You will rarely ever use the built-in standard `UITableViewCell` class.  In most
cases you will want to create your own types of cells that have components and
layout matching your needs.  As with any other view in Cocoa Touch, there are
three ways you can design your custom cell type: within a storyboard
itself via prototype cells, creating a separate NIB via Interface
Builder, or programmatically laying out your cell.

All three methods can be broken down into the following steps:

1.  Design your cell's layout and populating it with UI elements that
    configured.  This creates a template that can then be configured later on a
per row basis with different data.
2.  Create a class for type cell and associate it with the user interface for
    the cell.  This includes binding properties in your class to UI elements.
You will also need to expose properties that allow the user of the cell class to
update the appearance of the cell based on a given row's data.
3.  Register your cell type and give it a *reuse identifier*.
4.  Dequeue a cell instance using the reuse identifier and configure it to match
    a row's data.


### Using prototype cells
<!-- TODO: what about interface builder nib for view controller? -->
To use prototype cells you must be in the storyboard editor and have already
placed a table view in your view controller.  In order to create a prototype
cell you simply drag a `Table View Cell` from the Object Library onto your table
view.  You can now layout and add objects your prototype cell as you would with
any other view.

![Creating a Prototype Cell](http://i.imgur.com/nMFup96.gif)

Once you are satisfied with the design of your cell, you must create a custom
class and associate it with your UI template.  Select `File -> New -> File... ->
iOS -> Source -> Cocoa Touch Class`.  Create your class as a subclass of
`UITableViewCell`.  You must now associate your class with your prototype cell.
In the storyboard editor, select your prototype cell and then select the
Identity Inspector.  Set the `Custom Class` property of the prototype cell to
the name of the class you just created.

<a href="http://imgur.com/2r9tOJo"><img src="http://i.imgur.com/2r9tOJo.png" title="Setting Custom Class"/></a>

You will now be able to select your custom cell class in the Assistant Editor
(tuxedo mode) and connect IBOutlets from your prototype cell into your class as
you would with any other view.  Note that you must select the "content
view" of your prototype cell in order for the your custom cell class to
show up under the Assistant Editor's automatic matching.

<a href="http://imgur.com/Tkofhwo"><img src="http://i.imgur.com/Tkofhwo.gif" title="source: imgur.com" /></a>

One you are satisfied with the design of your cell and the corresponding code in
your custom class, you must register your cell for reuse by providing it with a
reuse identifier.  In the storyboard editor, select your prototype cell and then
select the Attributes Inspector.  Set the Identifier field (Reuse Identifier) to
a unique string that can be used to identify this type of cell.  One method you
can use to avoid name collisions&mdash;for example with cells you import from
external librarys&mdash;is to prefix your identifier with your organization
identifier (e.g. `com.codepath.mycellidenfier`).

<a href="http://imgur.com/nZdbnm5"><img src="http://i.imgur.com/nZdbnm5.png" title="source: imgur.com" /></a>

You can now use this identifier when calling `dequeueReusableCellWithIdentifier`
in your implementation of `cellForRowAtIndexPath`.  Notice that the compiler
cannot infer the type of your custom cell class from the reuse identifier and
you must explicitly cast the resulting object to the correct class.

```swift
import UIKit

class DemoPrototypeCell: UITableViewCell {
    @IBOutlet weak var cityLabel: UILabel!
    @IBOutlet weak var stateLabel: UILabel!
}
```

```swift
import UIKit

class ViewController: UIViewController, UITableViewDataSource {
    @IBOutlet weak var myFirstTableView: UITableView!

    let data = ["New York, NY", "Los Angeles, CA", "Chicago, IL", "Houston, TX",
        "Philadelphia, PA", "Phoenix, AZ", "San Diego, CA", "San Antonio, TX",
        "Dallas, TX", "Detroit, MI", "San Jose, CA", "Indianapolis, IN",
        "Jacksonville, FL", "San Francisco, CA", "Columbus, OH", "Austin, TX",
        "Memphis, TN", "Baltimore, MD", "Charlotte, ND", "Fort Worth, TX"]

    override func viewDidLoad() {
        super.viewDidLoad()
        myFirstTableView.dataSource = self
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = myFirstTableView.dequeueReusableCellWithIdentifier("com.codepath.DemoPrototypeCell", forIndexPath: indexPath) as DemoPrototypeCell
        let cityState = data[indexPath.indexAtPosition(1)].componentsSeparatedByString(", ")
        cell.cityLabel.text = cityState.first
        cell.stateLabel.text = cityState.last
        return cell
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return data.count
    }
}
```
Putting everything together we get a table that looks like this:

![Table With Custom Cells](http://i.imgur.com/B2pYrj4l.png)

### Creating a separate [NIB][nib] for your cell
<!--- TODO: may want to define what a nib is somewhere -->
<!--- TODO: write note about difference between XIB and NIB files -->

There may be times when you do not want to use prototype cells, but
still want to use Interface Builder to lay out the design of your custom
cell.  For example, you may be working on a project without storyboards
or you may want to isolate your custom cell's complexity from the rest
of your storyboard.  In these cases you will create a separate Interface
Builder file (NIB) to contain your custom cell's UI template.

The procedure in for working with a separate NIB is almost the same as
working with prototype cells.  You still design your cell in Interface
Builder and associate it with a custom cell class that inherits from
`UITableViewCell`.  The only difference is that you must now explicitly
load your NIB and register it for reuse.

You can create your NIB as you would any other view by going to `File ->
New -> File... -> iOS -> User Interface -> View` and then later create a
separate class and associate your Interface Builder view with your class
by setting the `Custom Class` property as you did with the prototype
cell.

However, *most of the time you will want to create your NIB and custom
class at once* by selecting `File -> New -> File... -> iOS -> Source ->
Cocoa Touch Class`.  You should then create you class as a subclass of
`UITableViewCell` and tick the box marked `Also create XIB file`.  This
will create a `.xib` and `.swift` file and automatically set the `Custom
Class` property of your table view cell to be the class you just
created.

<!--- TODO: picture of also create XIB file -->

You can now open the `.xib` file in Interface Builder, edit your view
and connect IBOutlets to your custom class using the Assistant Editor
(tuxedo) as you would any other view.

You do not need to set the reuse identifier attribute in Interface
Builder as we did for our prototype cell.  This is because once you are
ready to use your new cell you must explicitly load the NIB and register
it for reuse in your view controller:

```swift
import UIKit

class ViewController: UIViewController, UITableViewDataSource {
    @IBOutlet weak var myFirstTableView: UITableView!

    let data = ["New York, NY", "Los Angeles, CA", "Chicago, IL", "Houston, TX",
        "Philadelphia, PA", "Phoenix, AZ", "San Diego, CA", "San Antonio, TX",
        "Dallas, TX", "Detroit, MI", "San Jose, CA", "Indianapolis, IN",
        "Jacksonville, FL", "San Francisco, CA", "Columbus, OH", "Austin, TX",
        "Memphis, TN", "Baltimore, MD", "Charlotte, ND", "Fort Worth, TX"]

    override func viewDidLoad() {
        super.viewDidLoad()
        myFirstTableView.dataSource = self
        let cellNib = UINib(nibName: "DemoNibTableViewCell", bundle: NSBundle.mainBundle())
        myFirstTableView.registerNib(cellNib, forCellReuseIdentifier: "com.codepath.DemoNibTableViewCell")
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = myFirstTableView.dequeueReusableCellWithIdentifier("com.codepath.DemoNibTableViewCell", forIndexPath: indexPath) as DemoNibTableViewCell
        let cityState = data[indexPath.indexAtPosition(1)].componentsSeparatedByString(", ")
        cell.cityLabel.text = cityState.first
        cell.stateLabel.text = cityState.last
        return cell
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return data.count
    }
}
```

By default your NIB will be in the main resource bundle, although you
may change this in larger projects by editing your build steps.  The
code in `viewDidLoad` loads your nib by creating an instance of
[UINib][uinib] and registers it for reuse with the provided reuse
identifier.

[nib]: https://developer.apple.com/library/mac/documentation/General/Conceptual/DevPedia-CocoaCore/NibFile.html
[uinib]: https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UINib_Ref/index.html

### Laying out your cell programmatically

Finally, you may work with projects that do not use Interface Builder at
all.  In this case, you must lay out your custom cell programatically.
Create a custom cell class that subclasses `UITableViewCell`, but be
sure *not* to tick the `Also create XIB file` checkbox.

In order to be able register your custom cell for reuse you must
implement the [`init(style:reuseIdentifier:)`][initwithstyle] method
since this is the one that will be called by the `UITableView` when
instantiating cells.  As with any other `UIView`, you can also take
advantage of other entry points in the view's lifecycle (e.g.
[`drawRect:`][drawrect]) when programming your custom cell.

Once you are ready to use the cell, you must then register your custom
cell *class* for reuse in your view controller similarly to how we
registered the NIB for reuse above:


```swift
import UIKit

class DemoProgrammaticTableViewCell: UITableViewCell {
    let cityLabel = UILabel(), stateLabel = UILabel()

    override init(style: UITableViewCellStyle, reuseIdentifier: String?) {
        super.init(style: style, reuseIdentifier: reuseIdentifier)
        initViews()

    }

    required init(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
        initViews()
    }

    func initViews() {
        let (stateRect, cityRect) = frame.rectByInsetting(dx: 10, dy: 10).rectsByDividing(40, fromEdge:.MaxXEdge)
        cityLabel.frame = cityRect
        stateLabel.frame = stateRect
        addSubview(cityLabel)
        addSubview(stateLabel)
    }
}
```
```swift
import UIKit

class ViewController: UIViewController, UITableViewDataSource {
    @IBOutlet weak var myFirstTableView: UITableView!

    let data = ["New York, NY", "Los Angeles, CA", "Chicago, IL", "Houston, TX",
        "Philadelphia, PA", "Phoenix, AZ", "San Diego, CA", "San Antonio, TX",
        "Dallas, TX", "Detroit, MI", "San Jose, CA", "Indianapolis, IN",
        "Jacksonville, FL", "San Francisco, CA", "Columbus, OH", "Austin, TX",
        "Memphis, TN", "Baltimore, MD", "Charlotte, ND", "Fort Worth, TX"]

    override func viewDidLoad() {
        super.viewDidLoad()
        myFirstTableView.dataSource = self
        myFirstTableView.registerClass(DemoProgrammaticTableViewCell.self, forCellReuseIdentifier: "com.codepath.DemoProgrammaticCell")
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = myFirstTableView.dequeueReusableCellWithIdentifier("com.codepath.DemoProgrammaticCell", forIndexPath: indexPath) as DemoProgrammaticTableViewCell
        let cityState = data[indexPath.indexAtPosition(1)].componentsSeparatedByString(", ")
        cell.cityLabel.text = cityState.first
        cell.stateLabel.text = cityState.last
        return cell
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return data.count
    }
}
```

[initwithstyle]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/#//apple_ref/occ/instm/UITableViewCell/initWithStyle:reuseIdentifier:
[drawrect]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instm/UIView/drawRect:

## Variable height cells



## References

