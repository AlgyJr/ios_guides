
Table views are the center of many iOS applications and have many features to customize their appearance and behavior. The sections below cover basic as well as more custom table views.

<img src="http://i.imgur.com/rYbUIR1.png" />

Download the sample code [here](https://github.com/codepath/ios_guides/tree/master/demos/SimpleTableView).

### Basic Table View

#### Step 1: Create a view controller

Create a view controller, and drag a UITableView into the view, as shown below.

<img src="http://i.imgur.com/uDwEPMA.gif" />

#### Step 2: Create a table view outlet

Control-drag from the view to the implementation file to create an outlet to the UITableView.

#### Step 3: Set the datasource and delegate

Declare that the class implements the table view datasource and delegate protocols.

```
class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
  ...
}

```

In viewDidLoad, configure the datasource and delegate of the table view.

```
override func viewDidLoad() {
    super.viewDidLoad()

    tableView.delegate = self
    tableView.dataSource = self
}

```

#### Step 4: Implement the table view methods

There are many table view methods, but the only required methods are to set the number of rows for the table view and to return the cell for each row.

```
func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return 5
}

func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
    var cell = UITableViewCell()
    cell.textLabel.text = "This is row \(indexPath.row)"

    return cell
}

```

### Table View with Custom Cells

In practice, a UITableViewCell is rarely appropriate for your table views. Instead, you'll often need to create a custom cell with different subviews and custom highlight and selection effects. To create and use a custom cell, follow the steps below.

#### Step 1: Create the custom cell

In Storyboard, drag a UITableViewCell onto the table view. Create a new Swift file for the custom cell. It should be a subclass of UITableViewCell. Select the UITableViewCell in Storyboard and type in the name of the custom cell in the Identity Inspector.

![Identity](http://i.imgur.com/TMmgLJh.png)

Then, in the Attribute Inspector, set the Identifer of the custom cell.

![Attribute](http://i.imgur.com/WTGsMc8.png)

Create outlets to the custom cell class for any view you want to configure in code.

#### Step 2: Dequeue the cell

```
func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
    var cell = tableView.dequeueReusableCellWithIdentifier("YourCustomCell") as YourCustomCell

    // Configure YourCustomCell using the outlets that you've defined.

    return cell
}

```