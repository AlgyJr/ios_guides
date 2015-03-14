## Overview

This page covers the basics of working with an Xcode project. By the end of it, you should know what every file in your project does, and the core files found in every app.

## File Types

### Source: .swift

Each swift file ends in `.swift`. Objective-C uses two files: `.h` for headers, and `.m` for the corresponding implementation.

### Images: .xcassets

Say you have a home button in your app that is 44x44 pixels. On a retina display, you would want one at twice the resolution. If you want to take advantage of the larger iPad screen, you would want one at yet another set of dimensions. In total there are four image assets-- iPhone, iPhone Retina, iPad, and iPad Retina.

This can be daunting to track in code, so instead, it's managed by an **Asset Catalogue.** You add one image, "Home", which would contain four assets. At runtime, you would just ask for the "Home" image, and iOS will supply the appropriate asset for the current device.

The asset catalogue is the `Images.xcassets` folder. If you this folder in Xcode, it will bring up the *Asset Catalogue Editor* for managing assets.

<img src="https://i.imgur.com/gPEbazB.gif" />

### UI  Design: .storyboard, .xib

You can lay out your views with a drag and drop GUI, To save time and reduce boilerplate code. These designs are saved to a `.storyboard` or `.xib` file, which is loaded at runtime. The distinction between these file types is covered in a [[later guide||Storyboards]].

### Property Lists: .plist

[plist](http://en.wikipedia.org/wiki/Property_list) is a human-readable format that predates JSON, going all the way back to [NeXTSTEP](http://en.wikipedia.org/wiki/NeXTSTEP). It is the preferred file format of Apple.

## Core Files

### YourProjectName.xcodeproj

Your project file contains all the details of your project, such as the source files to compile and build settings.

### Info.plist

`Info.plist` contains [configuration data](https://developer.apple.com/library/iOs/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) read by iOS. For instance, you can set `UIInterfaceOrientation` to tell iOS you only support landscape orientation, and it will launch the app in a landscape orientation.

### AppDelegate.swift

This is the entry point for critical events in the life of your app. For instance, fill in `application(application, didFinishLaunchingWithOptions:options)` with any code you want run after your application finishes launching.

For more, read about [[App Architecture|Architecture]]

### Main.storyboard

This determines the UI displayed on launch. If you would rather set up your UI programmatically, for reasons covered in the [[Interface Builder|Interface Builder]] guide, you can configure it in your `Info.plist`, and write the setup code in `AppDelegate.swift`.

## Organization

The location of files in the project does not have to correlate with their location on disk. However, it's a good idea to keep things at least rudimentarily organized. It will depend on your team's conventions, but one layout might be:

```
ProjectName.xcodeproj
src/
resources/
config/
```

If you move a file on disk, the filename will turn red within in the [Navigation Panel](https://developer.apple.com/library/mac/recipes/xcode_help-general/AbouttheNavigatorArea/AbouttheNavigatorArea.html), because Xcode has lost track of it. You'll need to relink the file. Click the filename, and within the [Utility  Panel](https://developer.apple.com/library/mac/recipes/xcode_help-general/AbouttheUtilityArea/AbouttheUtilityArea.html), click the "Location" button, and navigate to the file's new location on disk.

## Targets

A basic project just produces an app, so the default project Xcode generates for you will be fine. An advanced project may produce other targets, such as static libraries, system extensions, alternative apps, test suites, and more.

For example, your shipping apps will commonly have two app targets:  the app you will ship to the App Store, and an internal version of the app for testing.  When you click the "Run" button in the [toolbar](https://developer.apple.com/library/mac/recipes/xcode_help-general/AbouttheWorkspaceWindowToolbar/AbouttheWorkspaceWindowToolbar.html), you want to launch the appropriate build. You can toggle this with the "Scheme Drop Down" to the right of the Run button.

# Further Reading

* [Xcode Basics](https://developer.apple.com/library/mac/recipes/xcode_help-general/_index.html): A quick overview of the Xcode UI
* [Xcode Overview](https://developer.apple.com/library/iOs/documentation/ToolsLanguages/Conceptual/Xcode_Overview/About_Xcode/about.html): An in-depth look at Xcode
* [iOS App Programming Guide](https://developer.apple.com/library/iOs/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Introduction/Introduction.html): A tour of all iOS fundamentals