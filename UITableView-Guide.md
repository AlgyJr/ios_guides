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
property must be set to an object that implements the [`UITableViewDelegate`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html) protocol.  This object controls the basic visual appearance of and user interactions with the table.

```swift
import UIKit

class ViewController: UIViewController, UITableViewDataSource {
    @IBOutlet weak var myFirstTableView: UITableView!

    let data = ["Alabama", "Alaska", "Arizona", "Arkansas", "California", "Colorado", "Connecticut", "Delaware", "Georgia"]

    override func viewDidLoad() {
        super.viewDidLoad()
        self.myFirstTableView.dataSource = self
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = UITableViewCell(style: .Default, reuseIdentifier: "myFirstUITableViewCell")
        cell.textLabel?.text = self.data[indexPath.indexAtPosition(1)]
        return cell
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return self.data.count
    }
}
```

## Reusing UITableViewCells
