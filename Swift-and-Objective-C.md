## Swift and Objective-C in the Same Project 

With some caveats, you can use Objective-C code in a Swift file and vice versa.

### Using Objective-C in Swift

In order to use Objective-C in a Swift file, you have to create an Objective-C bridging header. While you can create this manually, the easiest way is to let Xcode do it for you.

### Step 1: Create an Objective-C file

In your Xcode project, create a new file and select "Objective-C" file in the wizard. It doesn't matter what you name the file because you'll delete it later anyway. When Xcode prompts you to create the bridging header, you should accept.

![Bridging header](http://i.imgur.com/0rzQCPM.png)

### Step 2: Import Objective-C Headers

In the bridging header, import any Objective-C header file that you would like to access from Swift. Once you do that, the Objective-C objects are available for you to use in any Swift file using the Swift language syntax.

## References

- [Using Swift with Cocoa and Objective-C](https://developer.apple.com/library/ios/documentation/swift/conceptual/buildingcocoaapps/MixandMatch.html) (apple.com)