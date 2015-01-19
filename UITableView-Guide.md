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

1. The [`datasource`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instp/UITableView/dataSource) property must be set to an object that implements the [`UITableViewDataSource`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html) protocol.  This object is responsible for the content of the table including providing the actual [`UITableViewCells`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/) that will be shown.

2. The [`delegate`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instp/UITableView/delegate)
property must be set to an object that implements the
[`UITableViewDelegate`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html)
protocol.  This object controls the basic visual appearance of and user
interactions with the table.  It is not technically mandatory for you provide
your own `delegate`, but in practice you will almost always want to do something
that requires implementing your own `UITableViewDelegate`.

The following is the most basic way to set up a UITableView.

```swift
class ViewController: UIViewController, UITableViewDataSource {
    @IBOutlet weak var myFirstTableView: UITableView!

    let data = ["Alabama", "Alaska", "Arizona", "Arkansas", "California", "Colorado", "Connecticut", "Delaware", "Georgia"]

    override func viewDidLoad() {
        super.viewDidLoad()
        self.myFirstTableView.dataSource = self
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return self.data.count
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        // creates a new UITableViewCell for each cell displayed in the table
        // we will want to change this later so that the cells are reused
        let cell = UITableViewCell(style: .Default, reuseIdentifier: "myFirstUITableViewCell")
        cell.textLabel?.text = self.data[indexPath.indexAtPosition(1)]
        return cell
    }
}
```

Provided that the `@IBOutlet` `myFirstTableView` has been connected to a
`UITableView` in your storyboard, you will see something like this when running
the above code:

<!--- TODO: Link to documentation showing how to connect to IBOutlets -->
<!--- TODO: attach screenshot here -->

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

## Reusing UITableViewCells
