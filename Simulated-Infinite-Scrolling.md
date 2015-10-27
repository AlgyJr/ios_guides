In this guide, we will simulate an **Infinite Scrolling** experience. Simulated Infinite Scrolling allows a user to continually scroll through content, pausing briefly at the bottom of each scroll to simulate network loading. To achieve this effect, we will leverage the following concepts:
- [Using UIScrollView](https://guides.codepath.com/ios/Using-UIScrollView)
- [Using UIActivityIndicator View](https://guides.codepath.com/ios/Using-UIActivityIndicatorView)
- [Programmatically Creating Views](https://guides.codepath.com/ios/Programmatically-Creating-Views)  
  
![infinite scroll demo|220](http://i.imgur.com/tYSUuz3.gif)
  
### Step 1: Add and Configure UIActivityIndicatorView
The Activity Indicator will be positioned behind the ScrollView near the bottom of the screen. It will only be revealed when the ScrollView scrolls all the way to the bottom, given ScrollView content insets that we will set later.
- **Add a UIActivityIndicatorView** just **above** the ScrollView in the **Document Outline**, this will position the Activity Indicator **behind** the **ScrollView**. The ActivityIndicator should **NOT** be inside the scrollView, rather it should be a child of the ViewController's main **view**.  

![Add Activity Indicator](http://i.imgur.com/RgvknJF.gif)

![Doc Outline Activity Indicator|200](http://i.imgur.com/wNpCVNd.png)

- In the **Attributes Inspector** of the **Activity Indicator**, **Set the Behavior** to **Animating**. 

![Activity Indicator Animating](http://i.imgur.com/WPVwgK9.png)

### Step 2: Position the UIActivityIndicatorView
It is possible to get the Activity Indicator positioned near the bottom of the ViewController in Storyboard, however it can be tricky because dragging the Activity Indicator down will most likely embed it into the ScrollView, which we don't want. Instead, we will set the Activity Indicator position programmatically within the `viewDidLoad` method. 

- **Create an outlet for the Activity Indicator**. We will name ours, `infiniteLoadingIndicator`.
- **Set the Activity Indicator position** within the `viewDidLoad` method. In order to place it **65px** above the bottom of the main **view**, we can say, `infiniteLoadingIndicator.center.y = view.frame.height - 65`.
- **Set the ScrollView Content Inset**. In order for the ScrollView to scroll up enough to reveal our Activity Indicator, we need to set the content Inset for the bottom. You can do this in the ScrollView's **Size Inspector**, but since we are already setting things up in `viewDidLoad`, we will set the Content Inset for the bottom **programmatically**.

```swift
   // Position the Activity Indicator 65px above the bottom of the screen
   infiniteLoadingIndicator.center.y = view.frame.height - 65

   // Set the ScrollView bottom Content Inset to 130px
   feedScrollView.contentInset.bottom = 130
```

### Step 3: Register for Scroll Events 
We will need to interface with our ScrollView more programmatically in order to get **contentOffset** data as well as call a special ScrollView method when our ScrollView has come to a stop. To get his information from our ScrollView, we will first need to [register for Scroll Events](https://guides.codepath.com/ios/Using-UIScrollView#registering-for-scroll-events).
- [Set the ScrollView Delegate](https://guides.codepath.com/ios/Using-UIScrollView#step-1-set-the-scroll-view-delegate)
- [Declare the UIScrollViewDelegate](https://guides.codepath.com/ios/Using-UIScrollView#step-2-declaring-the-uiscrollviewdelegate)

### Step 4: Create a Variable to Keep Track Of ImageViews
We will be creating a new ImageView each time we scroll to the bottom of our ScrollView. In order to figure out where we should place the next ImageView as well as how much to adjust the contentSize of our ScrollView, we will need to keep track of how many ImageViews have been created. We will do this in a variable called, `numberOfImageViews`.

- Create a "global" variable, (up where your outlets are), to keep track of how many ImageViews have been added. Give it an initial value of 1.

```swift
   // Define variable to keep track of added ImageViews.
   // Initial Value of 1 represents the initial ImageView
   var numberOfImageViews: CGFloat = 1
```

### Step 5: Create and Configure a Method for When Scrolling has Stopped.
- We will employ a special ScrollView method, `scrollViewDidEndDecelerating` to tell us when the Scroll has come to a complete stop. You can put this method anywhere in the ViewController.

- When the ScrollView stops, we need to check if the ScrollView has indeed reached the bottom. We will use a conditional statement and the **contentOffset** of the ScrollView to help us tell if the ScrollView has scrolled all the way to the bottom. `if feedScrollView.contentOffset.y + feedScrollView.frame.size.height >= feedScrollView.contentSize.height { // ScrollView is at the bottom }`  

- We will add a [delay method](https://guides.codepath.com/ios/Calling-a-Method-After-Delay) to give the Activity Indicator a moment to animate before we add on the next ImageView to the ScrollView Content.

- When the delay is finished, we will [programmatically create a new Image View](https://guides.codepath.com/ios/Programmatically-Creating-Views), put another copy of our Feed Image in it, and position it directly below the previous ImageView. We will reference the variable we created to keep track of how many ImageViews have been created and multiply it by the height of our ImageView to figure out how far down to place it each time.

- Since new ImageViews are being added to our ScrollView, we will increase the **contentSize** of our ScrollView each time we add a new ImageView. Again, we will reference our `numberOfImageViews` variable, multiply it by the height of our new ImageView, and add that to the original contentSize.

- Finally, we will increase the value of our `numberOfImageViews` variable by 1

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
                // Increase the feedScrollView contentSize with each additional imageView added using
                feedScrollView.contentSize = CGSize(width: self.feedImageView.frame.size.width,
                    height: self.feedImageView.frame.size.height + self.numberOfImageViews * newImageView.frame.size.height)
                // add 1 to scrollViewCount
                self.numberOfImageViews += 1
            })
        }
    }
```