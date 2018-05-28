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

<a href="https://imgur.com/oJzRkUE"><img src="https://i.imgur.com/oJzRkUE.gif" title="source: imgur.com" /></a>

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
        scrollView.contentSize = CGSize(width: contentWidth, height: contentHeight)
        ...
    }
}
```

```objc
//  ViewController.h
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController
@property (weak, nonatomic) IBOutlet UIScrollView *scrollView;
@end

//  ViewController.m
@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    CGFloat contentWidth = self.scrollView.bounds.size.width;
    CGFloat contentHeight = self.scrollView.bounds.size.height * 3;
    self.scrollView.contentSize = CGSizeMake(contentWidth, contentHeight);
    // ...
}

@end
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
        scrollView.contentSize = CGSize(width: contentWidth, height: contentHeight)

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

```objc
//  ViewController.m
@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    CGFloat contentWidth = self.scrollView.bounds.size.width;
    CGFloat contentHeight = self.scrollView.bounds.size.height * 3;
    self.scrollView.contentSize = CGSizeMake(contentWidth, contentHeight);
    
    CGFloat subviewHeight = (CGFloat)120;
    CGFloat currentViewOffset = (CGFloat)0;
    
    while (currentViewOffset < contentHeight) {
        CGRect frame = CGRectInset(CGRectMake(0, currentViewOffset, contentWidth, subviewHeight), 5, 5);
        CGFloat hue = currentViewOffset/contentHeight;
        UIView *subview = [[UIView alloc]initWithFrame:frame];
        subview.backgroundColor = [UIColor colorWithHue:hue saturation:1 brightness:1 alpha:1];
        [self.scrollView addSubview:subview];
        
        currentViewOffset += subviewHeight;
    }
}

@end
```

Here's what our example looks like when running:
<a href="https://imgur.com/nLFfOM4"><img src="https://i.imgur.com/nLFfOM4.gif" title="source: imgur.com" /></a>

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

```objc
//  ViewController.m
@implementation ViewController

...

- (IBAction)didTapDownButton:(id)sender {
    CGPoint newOffset = CGPointMake(self.scrollView.contentOffset.x, self.scrollView.contentOffset.y + 300);
    [self.scrollView setContentOffset:newOffset animated:YES];
}
@end
```

<a href="https://imgur.com/qtu6gAP"><img src="https://i.imgur.com/qtu6gAP.gif" title="source: imgur.com" /></a>

### Scrolling to a subview
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

```objc
//  ViewController.m
@implementation ViewController

UIView *grayView;

- (void)viewDidLoad {
   
   ...

   grayView = [[UIView alloc]initWithFrame:CGRectMake(50, 620, self.scrollView.contentSize.width - 100, 150)];
   grayView.backgroundColor = UIColor.grayColor;
   [self.scrollView addSubview:grayView];
}

- (IBAction)didTapScrollButton:(id)sender {
    [self.scrollView scrollRectToVisible:grayView.frame animated:YES];
}
@end
```

<a href="https://imgur.com/uLlEhV5"><img src="https://i.imgur.com/uLlEhV5.gif" title="source: imgur.com" /></a>

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

```objc
//  ViewController.m
@implementation ViewController

...

- (void)viewDidLoad {
    [super viewDidLoad];
    
    CGFloat pageWidth = self.scrollView.bounds.size.width;
    CGFloat pageHeight = self.scrollView.bounds.size.height;
    
    self.scrollView.contentSize = CGSizeMake(3*pageWidth, pageHeight);
    self.scrollView.pagingEnabled = YES;
    self.scrollView.showsHorizontalScrollIndicator = NO;
    
    UIView *view1 = [[UIView alloc] initWithFrame:CGRectMake(0, 0, pageWidth, pageHeight)];
    view1.backgroundColor = UIColor.blueColor;
    UIView *view2 = [[UIView alloc] initWithFrame:CGRectMake(pageWidth, 0, pageWidth, pageHeight)];
    view2.backgroundColor = UIColor.orangeColor;
    UIView *view3 = [[UIView alloc] initWithFrame:CGRectMake(2*pageWidth, 0, pageWidth, pageHeight)];
    view3.backgroundColor = UIColor.purpleColor;
    
    [self.scrollView addSubview:view1];
    [self.scrollView addSubview:view2];
    [self.scrollView addSubview:view3];
}
@end
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

```objc
//  ViewController.h
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController <UIScrollViewDelegate>

@property (weak, nonatomic) IBOutlet UIScrollView *scrollView;
@property (weak, nonatomic) IBOutlet UIPageControl *pageControl;
- (IBAction)pageControlDidPage:(id)sender

@end

//  ViewController.m
@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    ...
    
    self.scrollView.delegate = self;
    self.pageControl.numberOfPages = 3;
}

- (IBAction)pageControlDidPage:(id)sender {
    CGFloat xOffset = self.scrollView.bounds.size.width * (CGFloat)self.pageControl.currentPage;
    [self.scrollView setContentOffset:CGPointMake(xOffset, 0) animated:YES];
}

- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView{
    self.pageControl.currentPage = (NSInteger)self.scrollView.contentOffset.x/self.scrollView.bounds.size.width;
}

@end
```

<a href="https://imgur.com/PFnCulU"><img src="https://i.imgur.com/PFnCulU.gif" title="source: imgur.com" /></a>

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

<a href="https://imgur.com/xmLhLet"><img src="https://i.imgur.com/xmLhLet.gif" title="source: imgur.com" /></a>

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
