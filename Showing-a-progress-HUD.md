### Overview

In mobile apps, it's helpful to give the user an indication that content is being loaded while the app sends a network request to get new data. A common method is to use a **Progress HUD (Heads Up Display)** while fetching the data. We can use CocoaPods to install a library that provides us with a convenient way to show a loading indicator while our network request is pending.

#### Sample Progress HUD:

![Progress HUD][progress-HUD]

### Setting Up the Podfile

Since this project previously did not use **[CocoaPods](http://guides.codepath.com/ios/CocoaPods)**, use `pod init` to create a template **Podfile**. It should contain some lines at the top, lets uncomment them so they look like below:

    platform :ios, "8.0"
    use_frameworks!

This tells CocoaPods that your project is targeting *iOS 8.0* and will be using *frameworks* instead of *static libraries* heading toward a more [complex][1] **Podfile** that works with *Swift*.

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

```swift
import UIKit
import MBProgressHUD

class ViewController: UIViewController {

    // ...

    func loadDataFromNetwork() {

        // Display HUD right before next request is made
        MBProgressHUD.showHUDAddedTo(self.view, animated: true)

        // ...

        let task : NSURLSessionDataTask = mySession.dataTaskWithRequest(request,
            completionHandler: { (data, response, error) in
            
            // Hide HUD once network request comes back (must be done on main UI thread)
            MBProgressHUD.hideHUDForView(self.view, animated: true)
            
            // ...

        });
        task.resume()
    }
}
```

[1]: https://guides.cocoapods.org/syntax/podfile.html

[cocoapods]: https://cocoapods.org/

[search-blank]: http://i.imgur.com/7MPsssu.png
[search-progress]: http://i.imgur.com/QYYJhY3.png
[progress-HUD]: http://i.imgur.com/8Kl9bQ1l.png