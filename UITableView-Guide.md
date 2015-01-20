In depth guide for using UITableView

## Overview

[`UITableViews`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html)
are one of the most commonly used views in iOS programming.  They are
used for displaying grouped lists of cells.  Here are some examples of
`UITableViews`:

<!--- TODO: Add some sample images of UITableViews -->

`UITableViews` can be highly performant, displaying thousands of rows (*cells*) of
data. They also have common behavior baked in, such as scrolling, "editing mode", and
the ability to animate the addition or removal of rows.

This guide will cover the fundamentals of using a tableviews.

## Your first `UITableView`

As with other views in the Cocoa Touch framework, in order to use a `UITableView`
you provide it with [delegates](http://en.wikipedia.org/wiki/Delegation_pattern)
that are able to answer questions about what to show in the table and how the
application should respond to certain user interactions with the table.

The `UITableView` has two delegates that you must provide by setting the
corresponding properties on your `UITableView` object.

1. The [`dataSource`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instp/UITableView/dataSource) property must be set to an object that implements the [`UITableViewDataSource`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html) protocol.  This object is responsible for the content of the table including providing the actual [`UITableViewCells`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/) that will be shown.

2. The [`delegate`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instp/UITableView/delegate)
property must be set to an object that implements the
[`UITableViewDelegate`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html)
protocol.  This object controls the basic visual appearance of and user
interactions with the table.  It is not technically mandatory for you provide
your own `delegate`, but in practice you will almost always want to do something
that requires implementing your own `UITableViewDelegate`.

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
<!--- TODO: attach screenshot here -->

__Notice that we set `self.myFirstTableView.dataSource = self`__ in the
`viewDidLoad` method.  A common error that will result in a blank or misbehaving
table or is to forget the `dataSource` or `delegate` property on your
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
[`numberOfSections`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instm/UITableView/numberOfSections)
method and possibly return different values in our
[`numberOfRowsInSection`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instm/UITableView/numberOfRowsInSection:)
method depending the `section` that was passed in.

2. `func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath:
   NSIndexPath) -> UITableViewCell` is responsible for returning a preconfigured
cell that will be used to render the row in the table specified by the
`indexPath`.   The `indexPath` is an
[`NSIndexPath`](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSIndexPath_Class/index.html)
of length 2.  It contains two integers: `indexPath.indexAtPosition(0)` is the
section number, and `indexPath.indexAtPosition(1)` is the row number in the
given section.

## Reusing `UITableViewCells`

The [`cellForRowAtIndexPath`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/occ/intfm/UITableViewDataSource/tableView:cellForRowAtIndexPath:)
method has to return an instance of `UITableViewCell` that is configured with
the data for the row specified by the indexPath.  In the above code we created a
new instance of the Cocoa Touch-provided `UITableViewCell` class for each call
to `cellForRowAtIndexPath`.  Since our table had only a few simple cells you
might not have noticed any appreciable performance drop.  However, in practice,
you will almost __never create a new cell object for each row__ due to
performance costs and memory implications.  This becomes especially important
once you start creating more complex cells or have tables with large numbers of
rows.

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

In `viewDidLoad` we call our `UITableView`'s [`registerClass:forCellReuseIdentifier:`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instm/UITableView/registerClass:forCellReuseIdentifier:)
to associate the built-in class `UITableViewCell` with the constant string
identifier `kCellIdentifier`.  Notice that we do not explicitly create an
instance here.  The `UITableView` will handle the creation of all cell objects
for us.

In `cellForRowAtIndexPath`, we call [`dequeueReusableCellWithIdentifier:forIndexPath:`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instm/UITableView/dequeueReusableCellWithIdentifier:forIndexPath:)
to obtain a pre-created instance of `UITableViewCell` and then we proceed to
populate this cell with the data for the given row before returning it.

#### Notes about the cell reuse pattern:

* When we explicitly instantiated each cell object in `cellForRowAtIndexPath` we
  were able to specify the cell `style: .Default'`.  When we called
`dequeueReusableCellWithIdentifier` there was no place to specify the style.
[In practice][cellstyle], you will want to create your own subclass of
`UITableViewCell` and add initialization common to all cells for the class in
the initializer.

* Any customization of the cell on a per row basis should be done in
`cellForRowAtIndexPath`.  When designing a custom cell class be sure to allow
access to the properties you need to change on a per row basis.  In this case
the built-in `UITableViewCell` gives us access to its `textLabel` so that we
are able to set a different text for each row.

* There are no guarantees on the state of the cell that is returned by
`dequeueReusableCellWithIdentifier`.  *The cell will not necessarily be in the
newly initialized state.*  In general, it will have properties that were
previously set when configuring it with the data of another row.  Be sure
reconfigure *all* properties to match the data of the current row!


## Custom Cells
How to make custom cells

## References
[cellstyle]: http://stackoverflow.com/questions/13174972/setting-style-of-uitableviewcell-when-using-ios-6-uitableview-dequeuereusablecel

