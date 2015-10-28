In this guide, we will simulate an **Infinite Scrolling** experience. Simulated Infinite Scrolling allows a user to continually scroll through content, pausing briefly at the bottom of each scroll to simulate network loading. To achieve this effect, we will leverage the following concepts:
- [Using UIScrollView](https://guides.codepath.com/ios/Using-UIScrollView)
- [Using UIActivityIndicator View](https://guides.codepath.com/ios/Using-UIActivityIndicatorView)
- [Programmatically Creating Views](https://guides.codepath.com/ios/Programmatically-Creating-Views)  
  
![infinite scroll demo|220](http://i.imgur.com/Ue5ZaZe.gif)
  
### Step 1: Position the UIActivityIndicatorView
We will put a UIActivityViewIndicator inside the UIScrollView, just below the feedImageView. It will always be animating and will only be revealed when we scroll to the very bottom. After a delay, a new ImageView will be added and the Activity Indicator will be moved down below the new ImageView.

- [Add an Activity Indicator to Storyboard](https://guides.codepath.com/ios/Using-UIActivityIndicatorView#step-1-add-activity-indicator-to-storyboard) inside of the ScrollView.
- **Set Behavior** to **Animating**, in the Attributes Inspector for the Activity Indicator.
- **Create an outlet for the Activity Indicator**. We will name ours, `infiniteLoadingIndicator`.
- **Set the Activity Indicator position** within the `viewDidLoad` method. We will place it 20px below the feed ImageView.
- **Set the ScrollView Content Inset**. In order for the ScrollView to scroll up enough to reveal our Activity Indicator, we need to set the content Inset for the bottom. You can do this in the ScrollView's **Size Inspector**, but since we are already setting things up in `viewDidLoad`, we will set the Content Inset for the bottom **programmatically**.

```swift
   // Position the Activity Indicator 20px below the bottom feed ImageView
   infiniteLoadingIndicator.center.y = feedImageView.image!.size.height + 20

   // Set the ScrollView bottom Content Inset to 130px
   feedScrollView.contentInset.bottom = 130
```

### Step 2: Register for Scroll Events 
We will need to interface with our ScrollView more programmatically in order to get **contentOffset** data as well as call a special ScrollView method when our ScrollView has come to a stop. To get his information from our ScrollView, we will first need to [register for Scroll Events](https://guides.codepath.com/ios/Using-UIScrollView#registering-for-scroll-events).
- [Set the ScrollView Delegate](https://guides.codepath.com/ios/Using-UIScrollView#step-1-set-the-scroll-view-delegate)
- [Declare the UIScrollViewDelegate](https://guides.codepath.com/ios/Using-UIScrollView#step-2-declaring-the-uiscrollviewdelegate)

### Step 3: Create a Variable to Keep Track Of ImageViews
We will be creating a new ImageView each time we scroll to the bottom of our ScrollView. In order to figure out where we should place the next ImageView as well as how much to adjust the contentSize of our ScrollView, we will need to keep track of how many ImageViews have been created. We will do this in a variable called, `numberOfImageViews`.

- Create a "global" variable, (up where your outlets are), to keep track of how many ImageViews have been added. Give it an initial value of 1.

```swift
   // Define variable to keep track of added ImageViews.
   // Initial Value of 1 represents the initial ImageView
   var numberOfImageViews: CGFloat = 1
```

### Step 4: Create and Configure a Method for When Scrolling has Stopped.
- We will employ a special ScrollView method, `scrollViewDidEndDecelerating` to tell us when the Scroll has come to a complete stop. You can put this method anywhere in the ViewController. 

- When the ScrollView stops, we need to check if the ScrollView has indeed reached the bottom. We will use a conditional statement and the **contentOffset** of the ScrollView to help us tell if the ScrollView has scrolled all the way to the bottom.   

- We will add a [delay method](https://guides.codepath.com/ios/Calling-a-Method-After-Delay) to give the Activity Indicator a moment to animate before we add on the next ImageView to the ScrollView Content.

- When the delay is finished, we will [programmatically create a new Image View](https://guides.codepath.com/ios/Programmatically-Creating-Views), put another copy of our Feed Image in it, and position it directly below the previous ImageView. We will reference the variable we created to keep track of how many ImageViews have been created and multiply it by the height of our ImageView to figure out how far down to place it each time.

- Move the Activity Indicator down below the new ImageView that was created.

- Since a new ImageViews have been added to our ScrollView, we need to increase the **contentSize** of our ScrollView to accommodate the new ImageView. Again, we will reference our `numberOfImageViews` variable, multiply it by the height of our new ImageView, and add that to the original contentSize.

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
                // Move Activity Indicator down below new ImageView
                self.infiniteLoadingIndicator.center.y = self.feedImageView.image!.size.height + 20 + self.numberOfImageViews * newImageView.image!.size.height
                // Increase the feedScrollView contentSize with each additional imageView added using
                feedScrollView.contentSize = CGSize(width: self.feedImageView.frame.size.width,
                    height: self.feedImageView.frame.size.height + self.numberOfImageViews * newImageView.frame.size.height)
                // add 1 to scrollViewCount
                self.numberOfImageViews += 1
            })
        }
    }
```