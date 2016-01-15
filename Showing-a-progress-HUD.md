We've created an app from the [table view guide][1] and call a *REST API*. To give the user some indication of the app loading content we'd like to include a progress HUD while fetching the latest stories. We can use CocoaPods to install a package that provides us with a convenient way to show a loading indicator while our network request is pending and dismiss the indicator once we are fully loaded.

### Setting Up the Podfile

Since this project previously did not use **[CocoaPods][2]**, use `pod init` to create a template **Podfile**. It should contain some lines at the top, lets uncomment them so they look like below:

    platform :ios, "8.0"
    use_frameworks!

This tells CocoaPods that your project is targeting *iOS 8.0* and will be using *frameworks* instead of *static libraries* heading toward a more [complex][3] **Podfile** that works with *Swift*.

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

**TIP:** the above is a shorthand to load up your new workspace in one line.

### Adding the Progress HUD

Finally we update our **View Controller** to display the progress HUD
while our network request is pending and dismiss the HUD once we receive
a response.

```swift
import UIKit

class ViewController: UIViewController, UITableViewDataSource {
    @IBOutlet weak var tableView: UITableView!
    var stories: [Story] = []

    override func viewDidLoad() {
        super.viewDidLoad()
        tableView.dataSource = self
        fetchStories()
    }

    func fetchStories() {
        MBProgressHUD.showHUDAddedTo(self.view, animated: true)
        Story.fetchStories({ (stories: [Story]) -> Void in
            dispatch_async(dispatch_get_main_queue(), {
                self.stories = stories
                self.tableView.reloadData()
                MBProgressHUD.hideHUDForView(self.view, animated: true)
            })
        }, error: nil)
    }
}
```

### Final Result

![Progress HUD][progress-HUD]

[1]: Table-View-Guide#example-load-data-from-a-rest-api-and-display-it-in-your-table
[2]: https://github.com/codepath/ios_guides/wiki/CocoaPods
[3]: https://guides.cocoapods.org/syntax/podfile.html

[cocoapods]: https://cocoapods.org/

[search-blank]: http://i.imgur.com/7MPsssu.png
[search-progress]: http://i.imgur.com/QYYJhY3.png
[progress-HUD]: http://i.imgur.com/8Kl9bQ1l.png