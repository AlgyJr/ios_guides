## Overview
CocoaPods packages 3rd-party frameworks and functionalities into "Pods." Cocoapods will automagicically download Pods and integrate them into your project. 

This page covers the basics of installing and adding CocoaPods to your project. By the end of it you will be able to add the most popular Pods to your project. 

### Popular CocoaPods

* `pod 'AFNetworking', '~> 2.0'` 
    - Gives UIImageView the super-power of loading web images (see example below)
    - Infamous for making network requests easy
* `pod 'MBProgressHUD', '~> 0.5'` - Displays a popup loading hud (see example below)

## Setup  
### Terminal
Terminal is how you'll install and setup CocoaPods. You can open Terminal in the Applications/Utilities folder. 

### Install Cocoapods
You can install CocoaPods simply by typing the following 3 commands in your Terminal. *To the right of # is a comment for your viewing pleasure only.* 

```
gem update --system            # Updates RubyGems to latest version, may require your password
sudo gem install cocoapods     # installs CocoaPods, requires your password
pod setup                      # updates the "Pods" CocoaPods knows about
```


## Adding Cocoapods to your project
We're going to add the AFNetworking Pod to give UIImageView a web-image-loading superpower! 
We'll also add the MBProgressHUD Pod so we can create a nice loading screen. 

### Step 1 - Set your Terminal's directory 
A Terminal window is just like a Finder window on your Mac. It's only ever associated with one folder at a time. That folder is known as the "Present Working Directory." 

To setup CocoaPods, you need set your Terminal's Present Working Directory to the folder containing your XCode Project.

* Open Terminal
* Type the characters `cd ` that's `cd` and a *(Space)*
* Use Finder to locate the folder that contains your `.xcodeproject` file
* Drag that folder to your open terminal, which should now look like `cd /Path/To/Your/Folder`, then hit your return key
* Ensure that terminal is set to the folder you expect.
    - To see what folder your terminal is currently set to type `pwd` into the Terminal.
    - *Double check that you haven't `cd`'d any folder contained inside your project folder, or your `.xcodeproject` file*

[Watch the cd gif here](http://i.imgur.com/SJ6tkPv.gif)

### Step 2 - Add your Podfile 
CocoaPods uses a text file named `Podfile` to define your project's dependencies. To add your Podfile:

* Type `pod init` into your terminal 
* Type `open -a XCode Podfile` and edit your `Podfile` in XCode
 
Your `Podfile` will probably look like this:
```
# Uncomment this line to define a global platform for your project
# platform :ios, '6.0'

target 'mySpecialProject' do

end

target 'mySpecialProjectTests' do

end
```

[Watch the add your Podfile gif here](http://i.imgur.com/Tlx88ZN.gif)

### Step 3 - Add your Pods and install
* Start by cleaning up your `Podfile` to make it look like this:
```
platform :ios, '8.0'

```

* Add rows for each Pod you're installing, then save:
```
platform :ios, '8.0'

pod 'AFNetworking', '~> 2.0'
pod 'MBProgressHUD', '~> 0.5'
```


* Next have CocoaPods download and integrate by typing the folowing into terminal, then the return key. 

```
pod install
```

[Watch the add Pods and install gif here](http://i.imgur.com/3nKJkHB.gif)

### Step 4 - Open your new Workspace
After completing succesfully, CocoaPods will also generate a new Workspace file for you. Your `.xcworkspace` file works just like your current `.xcproject` file, except it brings-in your CocoaPods as well. 

* Close your `.xcproject` file
* Open your new `.xcworkspace` file
    - You can find your new `.xcworkspace` file in your project folder, or 
    - Open it from the terminal with `open MySpecialProject.xcworkspace`
    - **Important**: From now on you should only open your `.xcworkspace` and **not** your `.xcodeproj`
* Ensure your project runs, making sure the iOS simulator you want is selected.

Note: If you later need to change your `Podfile` to bring-in new Pods, simply run `pod install` again and reopen `MySpecialProject.xcworkspace` to load the new dependencies.

### Step 5 - Bridging from Objective-C
All the libaries at the top of this page are written in Objective-C. If you want to use these libraries in your Swift project, you'll have to add a *Bridging-Header* for the Pods you want to use in your Swift project.

This can be done automatically by adding any Objective-C file, then using the Bridging-Header XCode automatically creates for us. 

* Go to `File -> New -> File ... -> iOS -> Source -> Objective-C File` and add a file with any name and any settings.
* XCode will *Would you like to configure an Objective-C bridging header?* The correct answer to this question is Yes. 
* Of the new files added, one of them will be called `MySpecialProject-Bridging-Header.h`, click on it to edit: 

```objective-c 
//
//  Use this file to import your target's public headers that you would like to expose to Swift.
//

#import <AFNetworking/AFNetworking.h>
#import <MBProgressHUD/MBProgressHUD.h>
```

**Congratulations, you've installed your Pods!**

Note: You can delete the other files created in this step, but keep `MySpecialProject-Bridging-Header.h`

[Watch the bridging gif here](http://i.imgur.com/gqPbeo5.gif)

### Step 6 - Use your Cocoapods! 
This is the part where you benefit from your work. Finally we update our view controller's logic to use these Pods. 

1. We'll display the progress HUD while our network request is pending and dismiss the HUD once we receive a response.
2. We'll use AFNetworking's superpower for UIImageViews to load our Movie's thubmnail image.

```swift
import UIKit

class ViewController: UIViewController, UITableViewDataSource {
    @IBOutlet weak var headerImageView : UIImageView!
    @IBOutlet weak var tableView: UITableView!

    var movie : Movie!
    var showtimes: [Showtime] = []

    override func viewDidLoad() {
        super.viewDidLoad()
        tableView.dataSource = self
        fetchShowtimes()

        headerImageView.setImageWithURL(NSURL(string: movie.photoUrl))
    }

    func fetchStories() {
        MBProgressHUD.showHUDAddedTo(self.view, animated: true)
        movie.fetchShowtimes({ (showtimes: [Showtime]) -> Void in
            dispatch_async(dispatch_get_main_queue(), {
                self.showtimes = showtimes
                self.tableView.reloadData()
                MBProgressHUD.hideHUDForView(self.view, animated: true)
            })
        }, error: nil)
    }

    ...
}
```

## FAQ: How do I know what to type in the Bridging Header or in the Podfile?
Answer: The Pod's developers will usually tell you what to write. 

 That said, it's our attempt with this document to give you a cheat sheet for the most important Pods. [Tweet us](twitter.com/codepath) if there's an important one not listed.

## Further Reading
* See Codepath's in-depth on [[CocoaPods]], which includes links to major CocoaPods directories. 
