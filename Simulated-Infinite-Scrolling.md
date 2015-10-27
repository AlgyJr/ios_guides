In this guide, we will simulate an **Infinite Scrolling** experience. To achieve this effect, we will leverage the following concepts:
- [Using UIScrollView](https://guides.codepath.com/ios/Using-UIScrollView)
- [Using UIActivityIndicator View](https://guides.codepath.com/ios/Using-UIActivityIndicatorView)
- [Programmatically Creating Views](https://guides.codepath.com/ios/Programmatically-Creating-Views)

### Step 1: Register for Scroll Events
We will need to use the contentOffset of the ScrollView to help us tell if the ScrollView has scrolled all the way to the bottom. In order to get the contentOffset from the ScrollView we will need to [register for Scroll Events](https://guides.codepath.com/ios/Using-UIScrollView#registering-for-scroll-events)

```swift
    // The scrollView has come to a complete stop, so do the following...
    func scrollViewDidEndDecelerating(feedScrollView: UIScrollView) {       
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