
UIScrollView can be used with or without Auto Layout and you set it up differently depending on whether Auto Layout is enabled. This covers how to set up UIScrollView when Auto Layout is disabled.

Note:

- Try enabling the bounces vertically checkbox to allow for vertical bouncing even when the scrolling is at the edge of the content.
- Try checking the "paging" property. If paging is on, the scroll view will act like the Photos app and snap to a grid.

### Step 1: Create an outlet for the Scroll View

In order to make the UIScrollView scroll, you have to manually tell it how tall and wide the content is. Before you can do that, you must create an outlet, as shown below.

![Creating Scroll View Outlet|600](http://i.imgur.com/9rErlXd.gif)

### Step 2: Set the content size

```swift
scrollView.contentSize = CGSize(width: 320, height: 1000)
```

If you want to embed a tall image into the scrollView, then you can set the contentSize to the size of the image, as below. This assumes that you've also created an outlet for the Image View and added it as a subview of the scroll view.

```swift
scrollView.contentSize = imageView.frame.size
```

## Other Tasks

### Registering for Scroll Events

#### Step 1: Set the scroll view delegate

Create an outlet for the UIScrollView and in `viewDidLoad`, set the delegate of the UIScrollView to self. This is telling the scroll view to call the view controller with the scroll view event methods. You will have a compile error on the line below until you complete step 2.

```swift
override func viewDidLoad() {
    super.viewDidLoad()

	scrollView.delegate = self
}
```

#### Step 2: Declaring the UIScrollViewDelegate

Create a comma-delimited list of all the delegates that your view controller supports.

```swift
class MyViewController: UIViewController, UIScrollViewDelegate {

	...
}
```

#### Step 3: Implement the desired event

If you Command-click on the UIScrolLViewDelegate, it will pull up the list of events for the UIScrollView. Choose the event that you want, and add the function to your view controller file. Be sure to remove the text `optional` and also delete the underscore symbol.

```swift
func scrollViewDidScroll(_ scrollView: UIScrollView) {
    // This method is called as the user scrolls
}

func scrollViewWillBeginDragging(_ scrollView: UIScrollView) {

}

func scrollViewDidEndDragging(_ scrollView: UIScrollView, willDecelerate decelerate: Bool) {
    // This method is called right as the user lifts their finger
}

func scrollViewDidEndDecelerating(_ scrollView: UIScrollView) {
    // This method is called when the scrollview finally stops scrolling.
}
```

### Zooming in scroll view

Scroll views support zooming in and out using the pinch gesture. In order to enable zooming, follow the two steps below.

#### Step 1: Choose minimum and maximum zoom levels

In Storyboard or programatically, configure the minimum and maximum allowable zoom levels. For example, if you choose a maximum zoom level of 3, that means you're allowing a user to zoom in by a factor of 3.

![Configuring zoom levels in Storyboard|600](http://i.imgur.com/q0pk6b1.png)

Optionally, you can choose to allow bouncing zoom when the user reaches the minimum and maximum zoom levels.

#### Step 2: Choose the view to zoom

As above, register the view controller to be the delegate of the scroll view. Then, implement the following method and return the view to be zoomed. This can be an image view or it can be an arbitrary UIView that contains many other views.

```swift
func viewForZooming(in scrollView: UIScrollView) -> UIView? {
    return imageView
}
```