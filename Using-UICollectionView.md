Collection views share similarities with table views, however collection views have the power to do multi-column layouts and much more! Collection views are the center of many iOS applications and have many features to customize their appearance and behavior. The sections below cover basic as well as more custom collection views.

<img src="https://i.imgur.com/9ZTVJFt.gif" width="250" />

Download the sample code [here]().

### Basic Collection View

#### Step 1: Add the Collection View to the View Controller

Create a view controller, and drag a UICollectionView into the view.

<img src="https://i.imgur.com/VBgfYwW.gif" width="500" />

#### Step 2: Configure Collection View settings

In the **Utilities** Pane.
- Set the Collection View Cell Size in the Size Inspector. I chose 120 x 120 to in order to fit 3 cells across an iPhone 6 screen.  
![Set cell size](https://i.imgur.com/DqsWoPu.png)
- Choose the Scroll Direction of Collection View.  
![Choose Scroll Direction](https://i.imgur.com/Sldr8wr.gif)

#### Step 3: Create a Collection View Outlet

Control-drag from the collection view to the View Controller file to create an outlet.

#### Step 4: Set the dataSource, delegate and flowLayout

Declare that the class implements the table view dataSource, delegate, and flowLayout protocols. Look for the class declaration at the top of your Swift file. Add `UICollectionViewDataSource`, `UICollectionViewDelegate` and `UICollectionViewDelegateFlowLayout` after `UIViewController`. Note that there will be an error that your class doesn't implement the required UICollectionViewDataSource functions. The error won't go away until you complete Step 4 below.

```swift
class YourViewController: UIViewController, UICollectionViewDataSource, UICollectionViewDelegate, UICollectionViewDelegateFlowLayout {
  ...
}
```

In viewDidLoad, configure the datasource and delegate of the collection view.

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    collectionView.delegate = self
    collectionView.dataSource = self
}
```

#### Step 5: Create the Custom Cell File

- Create a new Swift file for the custom cell. It should be a subclass of UICollectionViewCell.  
  
<img src="https://i.imgur.com/gP4bBm7.gif" width="500" />

#### Step 6: Link the Custom Cell File to the Cell in Interface Builder

- In the Identity Inspector, set the Class to the Custom Cell file you made 
- In the Attribute Inspector, set the Reuse Identifier. It's common to use the same name for this as you did for the Class.  
  
<img src="https://i.imgur.com/CtQ93d9.gif" width="250" />

#### Step 7: Create Outlets for Cell Views
Create outlets to the custom cell class for any view you want to configure in code.  
  
<img src="https://i.imgur.com/EnKIZPe.gif" width="500" />

#### Step 8: Implement the Required Collection View Methods

There are many collection view methods, but the only required methods are to set the number of items for the collection view and to return the cell for each item.

```swift
func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {   
    // Set the number of items in your collection view.
    return 20        
}
```

Use the `dequeueReusableCellWithReuseIdentifier()` method to access your reusable cell. In order to access properties and methods in your Custom Cell Swift file, you will need to cast your cell to be of type, `YourCustomCell` using `as! YourCustomCell`. 

```swift
func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
    // Access
    let cell = yourCollectionView.dequeueReusableCell(withReuseIdentifier: "YourCustomCell", for: indexPath) as! YourCustomCell
    // Do any custom modifications you your cell, referencing the outlets you defined in the Custom cell file.
    cell.backgroundColor = UIColor.whiteColor()
    cell.label.text = "item \(indexPath.item)"

    return cell
}
```

If you run your App at this point, you will notice that there is a big gap between in the middle. We will fix this in the next step using FlowLayout methods.  
  
<img src="https://i.imgur.com/3gUrmEb.gif" width="200" />  
  
#### Step 9: Implement the Flow Layout Methods

Adding the following Flow Layout methods allows you to adjust the space between the cells.

```swift
func collectionView(collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, minimumLineSpacingForSectionAtIndex section: Int) -> CGFloat {
        
    return 4
}
    
func collectionView(collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, minimumInteritemSpacingForSectionAtIndex section: Int) -> CGFloat {
        
    return 1
}
```
