![Refresh|250](http://i.imgur.com/wRED0TT.gif)

Pull to refresh has been a standard UI convention in iOS since early days of Twitter. It's easy to include the default pull to refresh control into any scroll view, including UIScrollView, UITableView, or UICollectionView.

### Step 1: Create the UIRefreshControl

It's useful to create the UIRefreshControl as an instance variable at the top of the class because you need to access it to stop the loading behavior.

```swift
var refreshControl: UIRefreshControl!
    
...

```

### Step 2: Implement onRefresh Method
We will call this method every time the refresh control is triggered. 

```swift
func onRefresh() {
   
}
```

### Step 3: Add the UIRefreshControl to the Scroll View

In the `viewDidLoad()` method, add the refresh control as a subview of the scroll view. It's best to insert it at the lowest index so that it appears behind all the views in the scroll view.

```swift   
refreshControl = UIRefreshControl()
refreshControl.addTarget(self, action: #selector(onRefresh), forControlEvents: .valueChanged)
scrollView.insertSubview(refreshControl, at: 0)
```

**Note:** If the above code is not compiling, it may be because you are using an older version of Xcode. In that case try:

```swift
refreshControl = UIRefreshControl()
refreshControl.addTarget(self, action: "onRefresh", forControlEvents: UIControlEvents.ValueChanged)
scrollView.insertSubview(refreshControl, atIndex: 0)
```

### Step 4: Use Delay to Simulate Network Loading

For prototyping, you can simulate network loading by canceling refreshing after a couple of seconds.

```swift
// Implement the delay method
func run(after wait: TimeInterval, closure: @escaping () -> Void) {
    let queue = DispatchQueue.main
    queue.asyncAfter(deadline: DispatchTime.now() + wait, execute: closure)
}
```

```swift
// Call the delay method in your onRefresh() method
func refresh() {
    run(after: 2) { 
       self.refreshControl.endRefreshing()
    }
}
```

For a detailed explanation on how to tie pull to refresh to data from UITableViews, see [[this guide|Table-View-Guide#adding-pull-to-refresh]].