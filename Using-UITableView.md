
Table views are the center of many iOS applications and have many features to customize their appearance and behavior. The sections below cover basic as well as more custom table views.

<img src="https://i.imgur.com/rYbUIR1.png" />

Download the sample code [here](https://github.com/codepath/ios_guides/tree/master/demos/SimpleTableView).

### 1. Basic Table View

#### Step 1: Create a view controller

Create a view controller, and drag a UITableView into the view, as shown below.

<img src="https://i.imgur.com/uDwEPMA.gif" />

#### Step 2: Create a table view outlet

Control-drag from the view to the implementation file to create an outlet to the UITableView.

#### Step 3: Set the datasource and delegate

Declare that the class implements the table view datasource and delegate protocols. Look for the class declaration at the top of your Swift or Objective-C file. Add `UITableViewDataSource` and `UITableViewDelegate` after `UIViewController`. Note that there will be an error that your class doesn't implement the required UITableViewDataSource functions. The error won't go away until you complete Step 4 below.

```swift
class YourViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
  ...
}

```
```objc
@interface YourViewController() <UITableViewDataSource, UITableViewDelegate>

...

@end
```

In viewDidLoad, configure the datasource and delegate of the table view.

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    tableView.delegate = self
    tableView.dataSource = self
}

```
```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.tableView.dataSource = self;
    self.tableView.delegate = self;
}
```

#### Step 4: Implement the table view methods

There are many table view methods, but the only required methods are to set the number of rows for the table view and to return the cell for each row.

```swift
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return 5
}

func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = UITableViewCell()
    cell.textLabel?.text = "This is row \(indexPath.row)"

    return cell
}

```
```objc
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return 5;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell = [[UITableViewCell alloc] init];
    cell.textLabel.text = [NSString stringWithFormat:@"This is row %ld", (long)indexPath.row];
    
    return cell;
}
```

### 2. Table View with Custom Cells

In practice, a UITableViewCell is rarely appropriate for your table views. Instead, you'll often need to create a custom cell with different subviews and custom highlight and selection effects. To create and use a custom cell, follow the steps below.

#### Step 1: Create the custom cell

In Storyboard, drag a UITableViewCell onto the table view. Create a new Swift file for the custom cell. It should be a subclass of UITableViewCell. Select the UITableViewCell in Storyboard and type in the name of the custom cell in the Identity Inspector.

![Identity](https://i.imgur.com/TMmgLJh.png)

Then, in the Attribute Inspector, set the Identifier of the custom cell.

![Attribute](https://i.imgur.com/WTGsMc8.png)

Create outlets to the custom cell class for any view you want to configure in code.

#### Step 2: Dequeue the cell

```swift
func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCell(withIdentifier: "YourCustomCell") as! YourCustomCell

    // Configure YourCustomCell using the outlets that you've defined.

    return cell
}

```

### 3. Passing Data from Table View Cells
  
<img src="https://i.imgur.com/LnuC2nk.gif" width="250"/>  
  
When a user taps a cell in your table view, we will often want to navigate to another view controller that contains information related to the cell that was tapped. In order to do this,  we will need to figure out which cell was tapped, and then pass the relevant data from that cell to the next view controller.  
  
The basic setup for this Use Case will include two UIViewControllers with a UINavigationController. 
- The first ViewController will contain a UITableView and a UITableViewCell with a UILabel inside it. We will refer to this ViewController as **CustomTableViewController**
- The Second ViewController will contain a UILabel. We will refer to this ViewController as **DetailViewController**
- We will Embed the **CustomTableViewController** in a **NavigationController** to enable push navigation between the CustomTableViewController and the DetailViewController. 

#### Step 1: Create a ViewController with a TableView

- [Create a basic Table View](https://guides.codepath.com/ios/Using-UITableView#basic-table-view) including the two required table view methods.
- Add a UILabel inside your cell  
<img src="https://i.imgur.com/weWzUm5.png" width="250"/>

#### Step 2: Create a Custom Cell

- [Set up a table view with Custom Cell](https://guides.codepath.com/ios/Using-UITableView#table-view-with-custom-cells)
- Create an outlet from the label in your prototype cell to your Custom Cell file.  
<img src="https://i.imgur.com/DdPMHDZ.gif " width="500"/> 
  
#### Step 3: Embed in Navigation Controller
In this Use Case, we will Embed in a **Navigation Controller** to our **TableViewViewController**.  
  
<img src="https://i.imgur.com/ueHh4h7.png" width="250" /> <img src="https://i.imgur.com/1Xw7uRM.png" width="300" />  
  
#### Step 4: Create the Detail View Controller
- Add a new UIViewController to the Storyboard to serve as the **Detail ViewController**.  
- Create a Custom Swift ViewController File called, **DetailViewController** and associate it with the Detail ViewController in Storyboard. [Create the Custom Swift View Controller](https://guides.codepath.com/ios/Creating-Custom-View-Controllers#step-2-create-the-swift-view-controller)  
<img src="https://i.imgur.com/ySXR8th.png" width="250" />  
  
- Add a UILabel to the Detail ViewController.
- Create an outlet from the Label in Storyboard to the **DetailViewController** File.  
<img src="https://i.imgur.com/4ecNlf8.gif" width="500"/>  

#### Step 5: Setup the Detail View Controller Outlets and Variables  
In this demo, we will simply display the index value in the label of the DetailViewController to prove that we know which cell was tapped. In a real application, the index could be used to pull out corresponding data from arrays or dictionaries.

- Create an **Instance Variable** to hold the **index data** that will be passed from the CustomTableViewController. 

```swift
var index: Int!
```    

- Set the text of the Label within the `viewDidLoad` method.

```swift
label.text = ("You tapped the cell at index \(index)")
```

#### Step 6: Create a Segue from the Cell to the Detail ViewController
Since we have embedded in a navigation controller, we will want to create a **Push Segue** from the **Cell** in the CustomTableViewController to the **DetailViewController**  
  
- ctrl + drag from the **Cell** in the Document Outline to the **DetailViewController** and choose **Push** to create a Push Segue.  
  
<img src="https://i.imgur.com/RWaTyXT.gif" width="500"/>  
  
At this Point, taping a cell in the tableView should take you to the detail ViewController, however you will notice that the detail ViewController's label displays an index of **nil**. This is because we haven't passed any data to the DetailViewController yet. We will handle that in the next steps.  
  
<img src="https://i.imgur.com/q5bxlrb.gif" width="200"/>  
 
#### Step 7: Pass the Data
We need to add a `prepare` method in our CustomTableViewController file. The `prepare` method is called right before any segue happens from that ViewController. This will give us the opportunity to pass any data we need to the DetailViewController right before the segue happens. 

Segues know where they are coming from, `segue.sourceViewController` and where they are going to, `segue.destinationViewController`. We can get in touch with the DetailViewController easily by referencing `segue.destinationViewController`, since that is where the Segue is going. If we want to access variable properties in the DetailViewController, we just need to add, `as! DetailViewController` to let swift know that we are not just taking about ANY old destinationViewController, we are talking about the one that is DetailViewController. At this point, we have full access to anything in the DetailViewController!  

- Add and configure the `prepare` method to the **CustomTableViewController** file.

```swift
override func prepare(for segue: UIStoryboardSegue, sender: AnyObject?) {
   
    // Get the index path from the cell that was tapped
    let indexPath = tableView.indexPathForSelectedRow
    // Get the Row of the Index Path and set as index 
    let index = indexPath?.row
    // Get in touch with the DetailViewController
    let detailViewController = segue.destinationViewController as! DetailViewController
    // Pass on the data to the Detail ViewController by setting it's indexPathRow value
    detailViewController.index = index          
}
```
  
Now that the Data is being passed, the Label inside the DetailViewController should reflect the index of the cell that you clicked on in the table view!  
  
<img src="https://i.imgur.com/LnuC2nk.gif" width="200"/>  

