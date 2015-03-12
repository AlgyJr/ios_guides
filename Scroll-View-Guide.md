<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Overview](#overview)
- [Basic Usage](#basic-usage)
  - [Step 1: Add scroll view to view hierarchy](#step-1-add-scroll-view-to-view-hierarchy)
  - [Step 2: Set content size](#step-2-set-content-size)
  - [Step 3. Add content subviews](#step-3-add-content-subviews)
- [Scrolling programatically](#scrolling-programatically)
  - [Set content offset](#set-content-offset)
  - [Scrolling to a a subview](#scrolling-to-a-a-subview)
- [Using paging mode](#using-paging-mode)
  - [Enabling paging](#enabling-paging)
  - [Adding a page control](#adding-a-page-control)
- [Pinch to zoom](#pinch-to-zoom)
- [Using scroll views with auto layout](#using-scroll-views-with-auto-layout)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Overview
Scroll views allow you to display content in an area on the screen that
is smaller than the size of the content.  They allow the user to pan
(scroll) and/or zoom the content.  The
[`UITableView`][uitableview] and [`UICollectionView`][uicollectionview]
are a subclasses of [`UIScrollView`][uiscrollview], so many of the
points below will apply to them as well.

[uitableview]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html
[uicollectionview]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/index.html#//apple_ref/occ/cl/UICollectionView
[uiscrollview]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIScrollView_Class/index.html

## Basic Usage
### Step 1: Add scroll view to view hierarchy
Add a scroll view to your view controller by dragging one from the
Object Library.  The size of the scroll view is the size of the area
that is visible to the user.

<a href="http://imgur.com/oJzRkUE"><img src="http://i.imgur.com/oJzRkUE.gif" title="source: imgur.com" /></a>

### Step 2: Set content size
Tell the scroll view about the size of your content area by setting the
`contentSize` property.  In this example the content has the same width
as the scroll view and three times its height.  The user will have to
scroll vertically to access the lower parts of the content area.

```swift
class ViewController: UIViewController {
    @IBOutlet weak var scrollView: UIScrollView!

    override func viewDidLoad() {
        super.viewDidLoad()

        let contentWidth = scrollView.bounds.width
        let contentHeight = scrollView.bounds.height * 3
        scrollView.contentSize = CGSizeMake(contentWidth, contentHeight)
        ...
    }
}
```


### Step 3. Add content subviews
What is shown in the scrollable content area of a scroll view is
determined by its subviews.  The frame of each subview is relative to
the top left of the content area.  A scroll view can contain more than
one subview.  In this example we add a series differently-colored
subviews.  Each subview has the same width as the scroll view, but we
offset the top of their frames so they do not overlap each other.

```swift
class ViewController: UIViewController {
    @IBOutlet weak var scrollView: UIScrollView!

    override func viewDidLoad() {
        super.viewDidLoad()

        let contentWidth = scrollView.bounds.width
        let contentHeight = scrollView.bounds.height * 3
        scrollView.contentSize = CGSizeMake(contentWidth, contentHeight)

        let subviewHeight = CGFloat(120)
        var currentViewOffset = CGFloat(0);

        while currentViewOffset < contentHeight {
            let frame = CGRectMake(0, currentViewOffset, contentWidth, subviewHeight).rectByInsetting(dx: 5, dy: 5)
            let hue = currentViewOffset/contentHeight
            let subview = UIView(frame: frame)
            subview.backgroundColor = UIColor(hue: hue, saturation: 1, brightness: 1, alpha: 1)
            scrollView.addSubview(subview)

            currentViewOffset += subviewHeight
        }
    }
}

```

Here's what our example looks like when running:
<a href="http://imgur.com/nLFfOM4"><img src="http://i.imgur.com/nLFfOM4.gif" title="source: imgur.com" /></a>

## Scrolling programatically
### Set content offset
You can programatically scroll the contents a scroll view by setting its
`contentOffset` property.

```swift
class ViewController: UIViewController {
    @IBOutlet weak var scrollView: UIScrollView!

    ...

    @IBAction func didTapDownButton(sender: AnyObject) {
        let newOffset = CGPointMake(scrollView.contentOffset.x, scrollView.contentOffset.y + 300)
        scrollView.setContentOffset(newOffset, animated: true)
    }
}

```

<a href="http://imgur.com/qtu6gAP"><img src="http://i.imgur.com/qtu6gAP.gif" title="source: imgur.com" /></a>

### Scrolling to a a subview
If you want to make sure a given view is visible on screen you can
you can use the `scrollRectToVisible` method.

```swift
class ViewController: UIViewController {
    @IBOutlet weak var scrollView: UIScrollView!
    var grayView: UIView!

    override func viewDidLoad() {
        ...
        grayView = UIView(frame: CGRectMake(50, 620, scrollView.contentSize.width - 100, 150))
        grayView.backgroundColor = UIColor.grayColor()
        scrollView.addSubview(grayView)
    }

    @IBAction func didTapScrollButton(sender: AnyObject) {
        scrollView.scrollRectToVisible(grayView.frame, animated: true)
    }

}
```

<a href="http://imgur.com/uLlEhV5"><img src="http://i.imgur.com/uLlEhV5.gif" title="source: imgur.com" /></a>

## Using paging mode
### Enabling paging
You can force the scroll view to to snap to "page" boundaries by setting
the `pagingEnabled` property.  The size of a page is equal to the size
of the scroll view.  You'll generally want to disable to scroll
indicators when paging is enabled.  Here we allow the user to
horizontally page through three views.

```swift
class ViewController: UIViewController, UIScrollViewDelegate {
    @IBOutlet weak var scrollView: UIScrollView!

    override func viewDidLoad() {
        super.viewDidLoad()

        let pageWidth = scrollView.bounds.width
        let pageHeight = scrollView.bounds.height

        scrollView.contentSize = CGSizeMake(3*pageWidth, pageHeight)
        scrollView.pagingEnabled = true
        scrollView.showsHorizontalScrollIndicator = false

        let view1 = UIView(frame: CGRectMake(0, 0, pageWidth, pageHeight))
        view1.backgroundColor = UIColor.blueColor()
        let view2 = UIView(frame: CGRectMake(pageWidth, 0, pageWidth, pageHeight))
        view2.backgroundColor = UIColor.orangeColor()
        let view3 = UIView(frame: CGRectMake(2*pageWidth, 0, pageWidth, pageHeight))
        view3.backgroundColor = UIColor.purpleColor()

        scrollView.addSubview(view1)
        scrollView.addSubview(view2)
        scrollView.addSubview(view3)

    }
}
```

### Adding a page control
The paging behavior is often combined with a `UIPageControl` that
displays a dot for each page and allows the user to switch pages by
tapping.  This behavior can be achieved by implementing the
`UIScrollViewDelegate` and adding an action handler for the page
control.

```swift
class ViewController: UIViewController, UIScrollViewDelegate {
    @IBOutlet weak var scrollView: UIScrollView!
    @IBOutlet weak var pageControl: UIPageControl!

    override func viewDidLoad() {
        ...
        scrollView.delegate = self
        pageControl.numberOfPages = 3
    }

    @IBAction func pageControlDidPage(sender: AnyObject) {
        let xOffset = scrollView.bounds.width * CGFloat(pageControl.currentPage)
        scrollView.setContentOffset(CGPointMake(xOffset,0) , animated: true)
    }

    func scrollViewDidEndDecelerating(scrollView: UIScrollView) {
        pageControl.currentPage = Int(scrollView.contentOffset.x / scrollView.bounds.width)
    }
}
```

<a href="http://imgur.com/PFnCulU"><img src="http://i.imgur.com/PFnCulU.gif" title="source: imgur.com" /></a>

## Pinch to zoom
Basic zooming behavior via pinch gestures is provided by the scroll view
if you implement the `viewForZoomingInScrollView` delegate method.
Since a scroll view can have more than one subview, this method will
identify which view will be zoomed when the pinch gesture fires.  You'll
also need to set the `minimumZoomScale` and `maximumZoomScale`.

```swift
class ViewController: UIViewController, UIScrollViewDelegate {
    @IBOutlet weak var scrollView: UIScrollView!
    var imageView: UIImageView!

    override func viewDidLoad() {
        super.viewDidLoad()
        scrollView.delegate = self
        scrollView.minimumZoomScale = 0.25
        scrollView.maximumZoomScale = 2

        let image = UIImage(named: "romanesco-broccoli")
        imageView = UIImageView(image: image)
        scrollView.contentSize = image!.size
        scrollView.addSubview(imageView)
        scrollView.zoomScale = 0.5
    }

    func viewForZoomingInScrollView(scrollView: UIScrollView) -> UIView? {
        return imageView
    }
}
```

<a href="http://imgur.com/xmLhLet"><img src="http://i.imgur.com/xmLhLet.gif" title="source: imgur.com" /></a>

## Using scroll views with auto layout
As noted [above](#basic-usage), there are two sizes that are relevant
when working with a scroll view:

1. the scroll view's (frame) size.  This determines how the scroll view
   fits inside its parent view and its size as it appears on the screen to the user.
2. the scroll view's _content size_.  This determines the size of
   available to the scrollable content within the scroll view.

In order to use a scroll view with auto layout you must still specify
_both_ of these sizes.

One approach to accomplishing this is to use auto layout constraints
_only_ to determine the scroll view's size relative to its parents and
neighbors.  The content size and size of subviews inside the scroll
view are still set programatically as we have been doing throughout this
guide.

A second approach uses only Auto Layout constraints.  In particular,
this means there must be a way for Auto Layout to specify the content
size of a scroll view.  Altogether, the constraints must be enough to
determine three things:

1.  The size of the scroll view.  You can use the pin and align tools to
    create constraints to parent and sibling views.  Unlike other views,
you cannot rely on the size of child views of the scroll view here.
2.  The content size of the scroll view.  This is specified by creating
    constraints between the scroll view's edges and the _subviews within_
the scroll view.  Note that these constraints have a different
meaning than normal constraints between a parent view and its child
views.  The only size being determined here is the _content size_ of the
scroll view.
3.  Finally you must set constraints to determine the size of the actual
    subviews containing the scrollable content.
**These constraints cannot depend on the edges of the scroll
view**&mdash;since as we just saw, they would then be interpreted as
constraints on the content size.  It is typical here to set a fixed size
for a single main scrollable subview and to place other views inside this
subview.

A comprehensive discussion of this topic can be found [here](https://developer.apple.com/library/ios/technotes/tn2154/_index.html).