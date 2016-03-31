
While UIPageViewController comes with its own UIPageControl, its often useful to add your own, so you have greater control over its placement. Also, sometimes you don't want a full UIPageViewController, but you just want a UIScrollView with paging enabled.

### Step 1: Add and configure UIScrollView

- Add a UIScrollview to the storyboard.
- Create an outlet from the Scroll View.
- Add the `UIScrollViewDelegate` protocol to your ViewController. ![ScrollViewDelegate gif](http://i.imgur.com/m8OftbU.png)
- within the `viewDidLoad` method, set the scroll view content size and scroll view delegate. Your content size will vary according to how big your scrollable area is.

```swift
scrollView.contentSize = CGSize(width: 960, height: 568)
scrollView.delegate = self
```
### Step 2: Add the UIPageControl

In the Storyboard, add a UIPageControl to the view controller. Often, you will place it on top of, not inside of, the UIScrollView.   

- Add a UIPageControl to the Storyboard.
- Create an outlet from the Page Control

### Step 3: Register for scroll events

Register for the UIScrollView scroll event, for example, you can update the UIPageControl every time the UIScrollView stops moving.

```swift
func scrollViewDidEndDecelerating(scrollView: UIScrollView!) {
    // Get the current page based on the scroll offset
    var page : Int = Int(round(scrollView.contentOffset.x / 320))
    
    // Set the current page, so the dots will update
    pageControl.currentPage = page
}
```

### Step 4: Customize Page Control Properties
You can configure the number of dots as well as the color of the unselected or selected dots.  

*Number of dots to display:* You can set the number of pages for the page control to show as dots.
If I had four pages, I would set...
```swift
pageControl.numberOfPages = 4
```

**Color of current page dot:**
```swift
pageControl.currentPageIndicatorTintColor = UIColor.redColor()
```

For more details, see [[Registering for Scroll Events|Using UIScrollView#heading-registering-for-scroll-events]].