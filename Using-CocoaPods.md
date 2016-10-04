## Overview
This guide covers 1) setting-up CocoaPods, and 2) adding and installing Pods into your Xcode project. By the end of it you'll be ready for guides on *actually using* the Pods you need. 

### Necessary knowledge

* Basic level in Xcode
* Novice level in Terminal 

## One-time Setup  

### Install CocoaPods

* Install CocoaPods by typing the following commands into Terminal

```
gem update --system
gem install -n /usr/local/bin cocoapods
pod setup
```

* Just close your Terminal window and re-open it to complete setup!

**Note: If your Terminal gets stuck on `pod setup`, see FAQ**

## Adding Cocoapods to your project

### Step 1 - Set your Terminal's directory 

First you need set your Terminal's Present Working Directory to the folder containing your XCode Project.

* Type the characters "`cd`" + *space*
* Drag the folder containing your `.xcodeproject` file to your terminal, then hit your return key

![Watch the cd gif here](http://i.imgur.com/SJ6tkPv.gif)

### Step 2 - Add your Podfile 

CocoaPods uses a text file named `Podfile` to define your project's Pods. To add your Podfile:

* Type `pod init` into your terminal 
* Type `open -a Xcode Podfile` and edit your `Podfile` in Xcode

![Watch the add your Podfile gif here](http://i.imgur.com/Tlx88ZN.gif)

### Step 3 - Add your Pods and install

* First, you can delete everything in this file
* Add `use_frameworks!`
* Add a row for each Pod you're installing, then save

```
use_frameworks!

pod 'MBProgressHUD'
pod 'AFNetworking', '~> 2.0'
```

**Note: Your Pods will be different.** These are two examples.

* Next have CocoaPods install typing the following into terminal

```
pod install
```

**Note: If `pod install` is taking more than 60 seconds, see FAQ**

![Watch the add Pods and install gif here](http://i.imgur.com/3nKJkHB.gif)

### Step 4 - Open your new Workspace file

After your first `pod install`, CocoaPods will create a new `.xcworkspace` file for you, which includes has your CocoaPods as well. **Only use your `.xcworkspace` from now on.**

* Close your `.xcproject` file
* Open your new `.xcworkspace` file, which you can find in your project's folder

If you later need to change your `Podfile` to bring-in new Pods, simply run `pod install` again.

### Step 5 - Importing your pod

In any Swift file where you want to use the library, you need to import it.

```swift
import UIKit
import MBProgressHUD
```

### Step 6 - Done! Use your CocoaPods! 

**We're done!** Now you can use your Pods– just follow the Pod maker's tutorials.

For example, now you can use your Pod in a ViewController. 

```swift
class ViewController: UIViewController {
    var progressHUD : MBProgressHUD!
    ...
}
```

**Note: Your Pods will be different.** This is one example.

## FAQ

### My terminal froze during `pod setup` or `pod install`! What do I do?

If you experience absurdly long/ several minute waiting on `pod setup`, or `pod install`, you may try the following:

* Close your Terminal window, and open a new one
* Type the following commands
 
```
pod repo remove master
pod setup
```

* Now you can go back to step one and try again!

### How do I know what to type in the Bridging Header or in the Podfile?

Answer: The Pod's developers will usually tell you what to write on their website.  

## Further Reading

* See Codepath's in-depth on [[CocoaPods]], which includes links to major CocoaPods directories. 
