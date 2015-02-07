<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Overview](#overview)
- [Installing Cocoapods](#installing-cocoapods)
- [Basic use](#basic-use)
- [Where to search for CocoaPods](#where-to-search-for-cocoapods)
- [Bridging from Objective-C](#bridging-from-objective-c)
- [Carthage: an alternative option for Swift projects](#carthage-an-alternative-option-for-swift-projects)
- [Example: using CocoaPods to integrate a progress HUD library](#example-using-cocoapods-to-integrate-a-progress-hud-library)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Overview
CocoaPods is a dependency management system for iOS (and other
Cocoa-based) projects.  It is very similar in function and usage to
[npm](https://www.npmjs.com/) for JavaScript and
[Bundler](http://bundler.io/) for Ruby.  CocoaPods can

1. download specific versions of libraries automatically
2. automatically resolve transitive dependencies
3. automatically integrate any libraries into the Xcode build settings
   for your project
4. help you package your library or component for redistribution

## Installing Cocoapods
CocoaPods is packaged as a Ruby gem.  Since Ruby comes with new OS X
installations, you can install CocoaPods simply by running the following
commands in a terminal.

```
gem update --system            # Updates RubyGems to latest version
sudo gem install cocoapods     # install CocoaPods gem
pod setup                      # clones the CocoaPods specs repo to ~/.cocoapods
```

If you for some reason need to perform a sudo-less install or need to
install a beta version of CocoaPods, a guide on how to do that can be
found [here](http://guides.cocoapods.org/using/getting-started.html).

## Basic use
CocoaPods uses a plain text file named `Podfile` to define your
project's dependencies and build targets.  If you're familiar
JavaScript or ruby, the format is similar to npm's `package.json` or
Bundler's `Gemfile`.

If you have already created a project and want to add dependencies using
CocoaPods, you should create a file named `Podfile` in the root
directory of your project.  This is the the parent directory of your
`YourProject.xcodeproj` directory.

The [`Podfile`](http://guides.cocoapods.org/using/the-podfile.html)
format has many commands but the two most important are `pod` which
specifies a dependency, and `platform` which defines the target
platform.  Here is a simple `Podfile`

```
platform :ios, '7.0'
pod 'AFNetworking', "~> 2.0"
```

This tells CocoaPods that we want to target iOS versions 7.0 and above.
It also says our project is dependent on the
[AFNetworking](https://github.com/AFNetworking/AFNetworking) library and
can use any version of AFNetworking between 2.0 up to but not including
3.0.

Once you have created your `Podfile` and defined the dependencies you
want to be pulled-in, you can have CocoaPods download and integrate them
for you by executing

```
pod install
```

in a terminal in the directory containing your `Podfile`.  CocoaPods
will download your dependencies and generate a `.xcworkspace` for your
project.  From now on you should only open `YourProject.xcworkspace` and
**not** `YourProject.xcodeproj`.  The former will have all the
dependencies already integrated.

If you later need to change or update your `Podfile` to reflect new
dependencies, simply run `pod install` again and reopen
`YourProject.xcworkspace` to load the new dependencies.

## Where to search for CocoaPods
The master [CocoaPods Specs
repository](https://github.com/CocoaPods/Specs) is the default
repository where CocoaPod will look for your dependencies.  Most
publically available pods will be published here.  The following sites
can be used to help locate the right library for your needs:

* [CocoaPods Search by Wantedly](http://cocoapods.wantedly.com/)
* [Official CocoaPods Site](http://cocoapods.org/)
* [CocoaControls](https://www.cocoacontrols.com/)

## Bridging from Objective-C
The current version of CocoaPods (0.35 as of this writing) does not have
first class support for Swift.  What this means in practice&mdash;if you
only wish to consume libraries in your Swift project&mdash;is that
you'll have to add an [Objective-C bridging header][bridgingheaders] for
any CocoaPod library you want to use in a Swift project.

[bridgingheaders]: https://developer.apple.com/library/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html

This can be done by `File -> New -> File ... -> iOS -> Source -> Header
File`.  You'll want to name this file something like
`YourProject-Bridging-Header.h`, but the name doesn't actually affect
the build process.  You'll need to specify this file as a bridging
header in your project's `Build Settings`.

<a href="http://imgur.com/aP32OZC"><img src="http://i.imgur.com/aP32OZC.png" title="Objective C Bridging" /></a>

You will then `#import` every Objective-C header you want to expose to
Swift in this file.

_NB: The beta version of [CocoaPods
0.36](http://blog.cocoapods.org/Pod-Authors-Guide-to-CocoaPods-Frameworks/)
does support Swift projects and is required in order to use CocoaPods
for certain Swift libraries like
[AlamoFire](https://github.com/Alamofire/Alamofire)_

## Carthage: an alternative option for Swift projects
An alternative depdency management system for Cocoa applications that
has gained some traction recently is
[Carthage](https://github.com/Carthage/Carthage).  Carthage officially
supports Swift and Frameworks.  However it does not automatically
integrate dependencies into your workspace and only supports iOS 8 and
above.  Many Swift only libraries provide a Carthage package.

## Example: using CocoaPods to integrate a progress HUD library
We'll extend our [NY Times Viewer example](Table-View-Guide#example-load-data-from-a-rest-api-and-display-it-in-your-table)
from the table view guide to
show a loading HUD when fetching the latest stories.  We can use
CocoaPods to install a library that provides us with a covenient way to
show a loading indicator while our network request is pending and
dismiss the indicator once we are fully loaded.

Since this project previously did not use CocoaPods, we created the
following `Podfile` in the root directory of the project

```
pod 'MBProgressHUD'
```

This tells CocoaPods that we to download and integrate the latest
version of [MBProgressHUD](https://github.com/jdg/MBProgressHUD) into
our project.  Notice that since we did not specify a target platform,
CocoaPods will infer this from what was already defined in our project.

After executing `pod install` we open up `NYTimesViewer.xcworkspace` and
add a bridging header `BridgingHeader.h` and configure it in our
project's build settings (see [above](#bridging-from-objective-c) for
how to do this).  The bridging header allows us to import the
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
