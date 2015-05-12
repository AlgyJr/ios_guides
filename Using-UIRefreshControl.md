
![Refresh|250](http://i.imgur.com/wRED0TT.gif)

Pull to refresh has been a standard UI convention in iOS since early days of Twitter. It's easy to include the default pull to refresh control into any scroll view, including UIScrollView, UITableView, or UICollectionView.

### Step 1: Create the UIRefreshControl

It's useful to create the UIRefreshControl as an instance variable at the top of the class because you need to access it to stop the loading behavior.

```
class MyViewController: UIViewController {
    var refreshControl: UIRefreshControl!
    
    ...

```

### Step 2: Add the UIRefreshControl to the Scroll View

In `viewDidLoad`, add the refresh control as a subview of the scrollview. It's best to insert it at the lowest index so that it appears behind all the views in the scrollview.

```
override func viewDidLoad() {
    super.viewDidLoad()
    
    refreshControl = UIRefreshControl()
    refreshControl.addTarget(self, action: "onRefresh", forControlEvents: UIControlEvents.ValueChanged)
    scrollView.insertSubview(refreshControl, atIndex: 0)
}

```

### Step 3: Implement the onRefresh Method

In Step 2 above, we said whenever the refreshControl changes state, it should call the `onRefresh` method. We need to define that method. For prototyping, you can simulate network loading by canceling refreshing after a couple of seconds.

```
func delay(delay:Double, closure:()->()) {
    dispatch_after(
        dispatch_time(
            DISPATCH_TIME_NOW,
            Int64(delay * Double(NSEC_PER_SEC))
        ),
        dispatch_get_main_queue(), closure)
}

func onRefresh() {
    delay(2, closure: {
        self.refreshControl.endRefreshing()
    })
}

```