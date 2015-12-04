Collection views share similarities with table views, however collection views have the power to do multi-column layouts and much more!


Collection views are the center of many iOS applications and have many features to customize their appearance and behavior. The sections below cover basic as well as more custom collection views.

![CollectionView Example]()

Download the sample code [here]().

### Basic Collection View

#### Step 1: Add the Collection View to the View Controller

Create a view controller, and drag a UICollectionView into the view.

![Drag Collection View onto the View Controller]()

#### Step 2: Create a collection view outlet

Control-drag from the view to the implementation file to create an outlet to the UICollectionView. We will name our outlet `collectionView` for this example.

#### Step 3: Configure Collection View settings
In the **Utilities** Pane.
- Set the Collection View Cell Size in the Size Inspector.  
![Set cell size](http://i.imgur.com/b3oGKIk.png)
- Choose the Scroll Direction of Collection View.  
![Choose Scroll Direction](http://i.imgur.com/Sldr8wr.gif)
#### Step 3: Set the dataSource, delegate and flowLayout

Declare that the class implements the table view dataSource, delegate, and flowLayout protocols. Look for the class declaration at the top of your Swift file. Add `UICollectionViewDataSource`, `UICollectionViewDelegate` and `UICollectionViewDelegateFlowLayout` after `UIViewController`. Note that there will be an error that your class doesn't implement the required UICollectionViewDataSource functions. The error won't go away until you complete Step 4 below.

```
class YourViewController: UIViewController, UICollectionViewDataSource, UICollectionViewDelegate, UICollectionViewDelegateFlowLayout {
  ...
}
```

In viewDidLoad, configure the datasource and delegate of the table view.

```
override func viewDidLoad() {
    super.viewDidLoad()

    collectionView.delegate = self
    collectionView.dataSource = self
}

```

#### Step 4: Implement the collection view methods

There are many collection view methods, but the only required methods are to set the number of items for the collection view and to return the cell for each item.

```
func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return 5
}

func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
    let cell = UITableViewCell()
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