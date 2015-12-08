
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

Declare that the class implements the table view datasource and delegate protocols. Look for the class declaration at the top of your Swift file. Add `UITableViewDataSource` and `UITableViewDelegate` after `UIViewController`. Note that there will be an error that your class doesn't implement the required UITableViewDataSource functions. The error won't go away until you complete Step 4 below.

```
class YourViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
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

Then, in the Attribute Inspector, set the Identifier of the custom cell.

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

### Passing Data from Table View Cells
When a user taps a cell in your table view, we will often want to navigate to another view controller that contains information related to the cell that was tapped. In order to do this,  we will need to figure out which cell was tapped, and then pass the relevant data from that cell to the next view controller.  
  
The basic setup for this Use Case will include two UIViewControllers with a UINavigationController. 
- The first ViewController will contain a UITableView and a UITableViewCell with a UILabel inside it. We will refer to this ViewController as **CustomTableViewController**
- The Second ViewController will contain a UILabel. We will refer to this ViewController as **DetailViewController**
- We will Embed the **CustomTableViewController** in a **NavigationController** to enable push navigation between the CustomTableViewController and the DetailViewController. 

#### Step 1: Create a ViewController with a TableView

- [Create a basic Table View](https://guides.codepath.com/ios/Using-UITableView#basic-table-view)
- Add a UILabel inside your cell  
<img src="http://i.imgur.com/weWzUm5.png" width="250"

#### Step 2: Create a Custom Cell

- [Set up a table view with Custom Cell](https://guides.codepath.com/ios/Using-UITableView#table-view-with-custom-cells)
- Create an outlet from the label in your prototype cell to your Custom Cell file.  
<img src="http://i.imgur.com/DdPMHDZ.gif " width="350" 
  
#### Step 3: Embed in Navigation Controller
In this Use Case, we will Embed in a **Navigation Controller** to our **TableViewViewController**.  
  
<img src="http://i.imgur.com/ueHh4h7.png" width="250" /> <img src="http://i.imgur.com/1Xw7uRM.png" width="300" />  
  
#### Step 4: Create the Detail View Controller  
