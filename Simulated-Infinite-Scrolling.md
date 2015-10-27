In this guide, we will simulate an **Infinite Scrolling** experience. Simulated Infinite Scrolling allows a user to continually scroll through content, pausing briefly at the bottom of each scroll to simulate network loading. To achieve this effect, we will leverage the following concepts:
- [Using UIScrollView](https://guides.codepath.com/ios/Using-UIScrollView)
- [Using UIActivityIndicator View](https://guides.codepath.com/ios/Using-UIActivityIndicatorView)
- [Programmatically Creating Views](https://guides.codepath.com/ios/Programmatically-Creating-Views)

### Step 2: Add and Configure UIActivityIndicatorView
The Activity Indicator will be positioned behind the ScrollView near the bottom of the screen. It will only be revealed when the ScrollView scrolls all the way to the bottom, given ScrollView content insets that we will set later.
- **Add a UIActivityIndicatorView** just **above** the ScrollView in the **Document Outline**, this will position the Activity Indicator **behind** the **ScrollView**. The ActivityIndicator should **NOT** be inside the scrollView, rather it should be a child of the ViewController's main **view**.  

![Add Activity Indicator](http://i.imgur.com/RgvknJF.gif)

![Doc Outline Activity Indicator|200](http://i.imgur.com/wNpCVNd.png)

- In the **Attributes Inspector** of the **Activity Indicator**, **Set the Behavior** to **Animating**. 

![Activity Indicator Animating](http://i.imgur.com/WPVwgK9.png)

### Step 3: Position the UIActivityIndicatorView
It is possible to get the Activity Indicator positioned near the bottom of the ViewController in Storyboard, however it can be tricky because dragging the Activity Indicator down will most likely embed it into the ScrollView, which we don't want.

- Create an outlet
- Set the Activity Indicator position in viewDidLoad


### Step 3: Register for Scroll Events 
We will need to interface with our ScrollView more programmatically in order to get **contentOffset** data as well as call a special ScrollView method when our ScrollView has come to a stop. To get his information from our ScrollView, we will first need to [register for Scroll Events](https://guides.codepath.com/ios/Using-UIScrollView#registering-for-scroll-events).
- [Set the ScrollView Delegate](https://guides.codepath.com/ios/Using-UIScrollView#step-1-set-the-scroll-view-delegate)
- [Declare the UIScrollViewDelegate](https://guides.codepath.com/ios/Using-UIScrollView#step-2-declaring-the-uiscrollviewdelegate)

We will need to use the **contentOffset** of the ScrollView to help us tell if the ScrollView has scrolled all the way to the bottom. Additionally, we will employ a special ScrollView method,  In order to get the contentOffset from the ScrollView we will need to 

```swift
    // The scrollView has come to a complete stop, so do the following...
    func scrollViewDidEndDecelerating(feedScrollView: UIScrollView) {       
        print("Scrolling Stopped")
        // If the scrollView offset + the scrollview height is greater than or equal to the height of the scrollView content, 
        // We have reached the bottom, so...
        if feedScrollView.contentOffset.y + feedScrollView.frame.size.height >= feedScrollView.contentSize.height {
            print("You reached the bottom")
            // Delay for 1 second then...
            delay(1, closure: { () -> () in
                // Create frame for new imageView, same as feed ImageView
                let newFrame = self.feedImageView.frame
                // Create new ImageView with newFrame
                let newImageView = UIImageView(frame: newFrame)
                // Create new image, same as feed Image
                let newImage = UIImage(named: "home_feed")
                // Set new ImageView image to newImage
                newImageView.image = newImage
                // Position the new imageView below the previous image view
                newImageView.frame.origin.y = self.feedImageView.frame.origin.y + self.numberOfImageViews * self.feedImageView.frame.size.height
                // Add imageView to scrollView
                feedScrollView.addSubview(newImageView)
                // Increase the feedScrollView contentsize with each additional imageView added using
                feedScrollView.contentSize = CGSize(width: self.feedImageView.frame.size.width,
                    height: self.feedImageView.frame.size.height + self.numberOfImageViews * newImageView.frame.size.height)
                // add 1 to scrollViewCount
                self.numberOfImageViews += 1
            })
        }
    }
```