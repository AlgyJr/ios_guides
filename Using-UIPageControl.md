
While UIPageViewController comes with its own UIPageControl, its often useful to add your own, so you have greater control over its placement. Also, sometimes you don't want a full UIPageViewController, but you just want a UIScrollView with paging enabled.

### Step 1: Add the UIPageControl

In the Storyboard, add a UIPageControl to the view controller. Often, you will place it on top of, not inside of, the UIScrollView. You can configure the number of dots as well as the color of the unselected or selected dots.

### Step 2: Register for scroll events

Register for the UIScrollView scroll event, for example, you can update the UIPageControl every time the UIScrollView stops moving.

```
func scrollViewDidEndDecelerating(scrollView: UIScrollView!) {
    // Get the current page based on the scroll offset
    var page : Int = Int(round(scrollView.contentOffset.x / 320))
    
    // Set the current page, so the dots will update
    pageControl.currentPage = page
}


```

For more details, see [[Registering for Scroll Events|Using UIScrollView#heading-registering-for-scroll-events]].