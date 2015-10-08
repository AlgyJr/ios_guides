We'll extend our [NY Times Viewer example](Table-View-Guide#example-load-data-from-a-rest-api-and-display-it-in-your-table)
from the table view guide to
show a loading HUD when fetching the latest stories.  We can use
CocoaPods to install a library that provides us with a convenient way to
show a loading indicator while our network request is pending and
dismiss the indicator once we are fully loaded.

Since this project previously did not use CocoaPods, we created the
following `Podfile` in the root directory of the project

```
pod 'MBProgressHUD'
```

This tells CocoaPods that we want to download and integrate the latest
version of [MBProgressHUD](https://github.com/jdg/MBProgressHUD) into
our project.  Notice that since we did not specify a target platform,
CocoaPods will infer this from what was already defined in our project.

After executing `pod install` we open up `NYTimesViewer.xcworkspace` and
add a bridging header `BridgingHeader.h` and configure it in our
project's build settings.  The bridging header allows us to import the
`MBProgressHUD.h` header.


```objective-c
#ifndef NYTimesViewer_BridgingHeader_h
#define NYTimesViewer_BridgingHeader_h

#import <MBProgressHUD.h>

#endif
```

Finally we update our view controller's logic to display the progress HUD
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

    ...
}
```
<a href="http://imgur.com/8Kl9bQ1"><img src="http://i.imgur.com/8Kl9bQ1l.png" title="Progress HUD" /></a>
