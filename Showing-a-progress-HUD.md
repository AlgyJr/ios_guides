### Overview

In mobile apps, it's helpful to give the user an indication that content is being loaded while the app sends a network request to get new data. A common method is to use a **Progress HUD (Heads Up Display)** while fetching the data. We can use [CocoaPods](http://guides.codepath.com/ios/CocoaPods) to install a library that provides us with a convenient way to show a loading indicator while our network request is pending.

#### Sample Progress HUD

![Progress HUD][progress-HUD]

### Setting Up the Podfile

We'll start by doing `pod init` to create a template **Podfile**. It should contain some lines at the top similar to the below:
    
    # Uncomment the next line to define a global platform for your project
    # platform :ios, "9.0"
    ...
    use_frameworks!

This tells CocoaPods that your project will be using *frameworks* instead of *static libraries* heading toward a more [complex][1] **Podfile** that works with *Swift*.

You could also include the line **below** to supress any warnings from *Pods* we install if you're getting a lot of noise from them:

    inhibit_all_warnings!

### Installing the Pod

Next navigate to the [CocoaPods website][cocoapods] where they have a directory of different *PodSpecs*.

![search-blank][search-blank]

A quick search for **"Progress"** or **"Loading"** finds us a few different *Pods* we could use, in this case we're going to install **MBProgressHUD**.

![search-progress][search-progress]

**Edit your Podfile** to include the new *Pod* we found:

    pod 'MBProgressHUD'

Notice that since we did not specify a target platform, *CocoaPods* will infer this from what was already defined in our project. Then we download the package and incorporate it into our project with the following commands in the **Terminal**:

    pod install && open *.xcworkspace

**Tip:** the above is a shorthand to load up your new workspace in one line.

### Adding the Progress HUD

Finally we update our **View Controller** to display the progress HUD
while our network request is pending and dismiss the HUD once we receive
a response.

**NOTE: The below code shows the implementation of the Progress HUD in a sample network request. If your aim is to add the Progress HUD to an existing network request already in your project, you will only need to add `MBProgressHUD.showAdded(to: self.view, animated: true)` at the beginning of the request and `MBProgressHUD.hide(for: self.view, animated: true)` in the completion handler**

```swift
import UIKit
import MBProgressHUD

class ViewController: UIViewController {

    // ...

    func loadDataFromNetwork() {

        // ... Create the NSURLRequest (myRequest) ...

        // Configure session so that completion handler is executed on main UI thread
        let session = NSURLSession(
            configuration: NSURLSessionConfiguration.defaultSessionConfiguration(),
            delegate:nil,
            delegateQueue:NSOperationQueue.mainQueue()
        )

        // Display HUD right before the request is made
        MBProgressHUD.showAdded(to: self.view, animated: true)

        let task : NSURLSessionDataTask = session.dataTaskWithRequest(myRequest,
            completionHandler: { (data, response, error) in
            
            // Hide HUD once the network request comes back (must be done on main UI thread)
            MBProgressHUD.hide(for: self.view, animated: true)
            
            // ... Remainder of response handling code ...

        });
        task.resume()
    }
}
```

[1]: https://guides.cocoapods.org/syntax/podfile.html

[cocoapods]: https://cocoapods.org/

[search-blank]: https://i.imgur.com/7MPsssu.png
[search-progress]: https://i.imgur.com/QYYJhY3.png
[progress-HUD]: https://i.imgur.com/8Kl9bQ1l.png
