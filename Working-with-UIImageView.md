## Overview

Typically images are displayed using the built-in [UIImageView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImageView_Class). UIImageView supports both displaying a single image as well as animating a series of images.

## Usage

With Interface Builder it's pretty easy to add and configure a UIImageView. The first step is to drag the UIImageView onto your view.

![Add UIImageView|700](http://i.imgur.com/R3gk3Iq.gif)

Then open the UIImageView properties pane and select the image asset (assuming you have some images in your project). You can also configure how the underlying image is scaled to fit inside the UIImageView.

![Configure UIImageView|700](http://i.imgur.com/GrS8WjX.gif)

### Scale Types

### Supporting Multiple Screen Densities

### Working with UIImages

## Loading Images from the Network with AFNetworking

The built-in UIImageView works great when the **image is locally available**, but doesn't have great support when the image needs to be downloaded over the network. This is where AFNetworking comes in handy. The following steps walk through how to pull in and use AFNetworking for **working with images over the network**.

### Setup AFNetworking

First, we need to use [[CocoaPods]] to pull in AFNetworking (`pod 'AFNetworking'`).

After a `pod install`, we can now `import AFNetworking` and use the additional methods that are available for UIImageView:

```swift
import AFNetworking

class myViewController {
    
    // Outlet for ImageView
    @IBOutlet weak var myImageView: UIImageView!
    
    override func viewDidLoad() {
        super.viewDidLoad()

        let myImageUrlString = "https://i.imgur.com/tGbaZCY.jpg"

        // AFNetworking extension to UIImageView that allows
        // specifying a URL for the image
        // Swift 3 should use URL instead of NSURL
        myImageView.setImageWithURL(URL(string: myImageUrlString)!)
    }
}
```

### Improving the User Experience

There are a couple of enhancements that can be done when working with images being pulled from the network to improve the user experience.

#### Fading in an Image Loaded from the Network

It can be a little jarring for the user to have images pop into place once they are downloaded from the network. To prevent this, it's a good idea to fade images in as they are downloaded. 

```swift
let imageUrl = "https://i.imgur.com/tGbaZCY.jpg"
let imageRequest = URLRequest(url: URL(string: imageUrl)!)

self.myImageView.setImageWithURLRequest(
    imageRequest,
    placeholderImage: nil,
    success: { (imageRequest, imageResponse, image) -> Void in
        
        // imageResponse will be nil if the image is cached
        if imageResponse != nil {
            print("Image was NOT cached, fade in image")
            self.myImageView.alpha = 0.0
            self.myImageView.image = image
            UIView.animateWithDuration(0.3, animations: { () -> Void in
                self.myImageView.alpha = 1.0
            })
        } else {
            print("Image was cached so just update the image")
            self.myImageView.image = image
        }
    },
    failure: { (imageRequest, imageResponse, error) -> Void in
        // do something for the failure condition
})
```

#### Loading a Low Resolution Image followed by a High Resolution Image

Since pulling down high resolution images over the network takes time, it's common to first show a low resolution image so the user sees something in the UIImageView and then load the higher resolution image immediately afterwards.

```swift
let smallImageRequest = URLRequest(url: URL(string: smallImageUrl)!)
let largeImageRequest = URLRequest(url: URL(string: largeImageUrl)!)

self.myImageView.setImageWithURLRequest(
    smallImageRequest,
    placeholderImage: nil,
    success: { (smallImageRequest, smallImageResponse, smallImage) -> Void in
        
        // smallImageResponse will be nil if the smallImage is already available
        // in cache (might want to do something smarter in that case).
        self.myImageView.alpha = 0.0
        self.myImageView.image = smallImage;
        
        UIView.animateWithDuration(0.3, animations: { () -> Void in
            
            self.myImageView.alpha = 1.0
            
            }, completion: { (sucess) -> Void in
                
                // The AFNetworking ImageView Category only allows one request to be sent at a time
                // per ImageView. This code must be in the completion block.
                self.myImageView.setImageWithURLRequest(
                    largeImageRequest,
                    placeholderImage: smallImage,
                    success: { (largeImageRequest, largeImageResponse, largeImage) -> Void in
                        
                        self.myImageView.image = largeImage;
                        
                    },
                    failure: { (request, response, error) -> Void in
                        // do something for the failure condition of the large image request
                        // possibly setting the ImageView's image to a default image
                })
        })
    },
    failure: { (request, response, error) -> Void in
        // do something for the failure condition
        // possibly try to get the large image
})
```

## References
* https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImageView_Class