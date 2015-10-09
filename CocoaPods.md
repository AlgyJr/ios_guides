## Overview
CocoaPods is a dependency management system for iOS (and other
Cocoa-based) projects.  It is very similar in function and usage to
[npm](https://www.npmjs.com/) for JavaScript and
[Bundler](http://bundler.io/) for Ruby.

## Installing CocoaPods
CocoaPods is packaged as a Ruby gem. Since Ruby comes with new OS X installations, you can install CocoaPods simply by running the following commands in a terminal:

```
gem update --system            # Updates RubyGems to latest version
sudo gem install cocoapods     # Install CocoaPods gem
pod setup                      # Clones the CocoaPods specs repo to ~/.cocoapods
```

Note: There are alternate instructions if you need to perform a [sudo-less install](http://guides.cocoapods.org/using/getting-started.html#sudo-less-installation) or [install a beta version](http://guides.cocoapods.org/using/getting-started.html#updating-cocoapods) of CocoaPods. 

## Adding a Pod
You can find available Pods using the search box on the [official CocoaPods site](http://cocoapods.org/) or using the [Wantedly search](http://cocoapods.wantedly.com/). When you find a pod you want to add to your project, follow these steps:

1. Create the `Podfile` (only needs to be done once per project):
   - Run `pod init` from a terminal (in the root directory of your project)
   - This will create a plain text file named `Podfile`.
2. Add the required dependency to the `Podfile`. In the case below, we'll add AFNetworking:
      ```ruby
      # Podfile

      # This tells CocoaPods we want to target iOS versions 8.0 and above.
      platform :ios, '8.0'
      
      target 'MyApp' do

      # This pulls in the latest version of AFNetworking >= 2.6 but less than 3.0.
      pod 'AFNetworking', '~> 2.6'

      end
      ```
   - Refer to the [podfile documentation](https://guides.cocoapods.org/using/the-podfile.html) to see versioning options and more complex use cases.
3. Download and integrate the dependencies into your project:
   - From a terminal, run `pod install`
4. Open the Xcode workspace:
   - Close the project (`MyApp.xcodeproject`) if you have it open and open the workspace (`MyApp.xcworkspace`).
   - CocoaPods creates an additional project for all the dependencies so you need to open the `.xcworkspace` from now on (which will contain your original project and the pods project).

## Swift Support
CocoaPods version 0.36 was the first version to add [support for Swift](http://blog.cocoapods.org/CocoaPods-0.36) with the `use_frameworks!` directive.

As long as your app targets iOS8+, you can enable Swift support by adding the `use_frameworks!` directive to your `Podfile`:
```ruby
platform :ios, '8.0'
use_frameworks!    # Instruct CocoaPods to use dynamic frameworks instead of static libraries for Pods
...
```

### Consuming Libraries written in Swift
With the `use_frameworks!` directive, you can now consume Swift libraries using CocoaPods! After you've added `use_frameworks!` to your `Podfile`, you can directly import Swift libraries from your Swift code using the framework name (i.e. `import AFNetworking`).

### Consuming Objective-C Libraries from Swift
With the `use_frameworks!` directive, you can also consume Objective-C libraries from your Swift code without the need of a bridging header anymore. You'll be able to directly import the framework (i.e. `import AFNetworking`).

**Potential Issue:** Unfortunately, all Objective-C Pods haven't been updated to work with the new dynamic frameworks behavior. You might come across certain Objective-C Pods that no longer build after adding the `use_frameworks!` directive. In these cases you can either not use the `use_frameworks!` directive (you'll now need to create a [[bridging header|Swift-ObjectiveC-Interoperability#the-objective-c-bridging-header]]) or manually edit the offending Pod to help it find the headers (as done in the [linked issue](https://github.com/bdbergeron/BDBOAuth1Manager/issues/35)).

## Alternatives to CocoaPods
[Carthage](https://github.com/Carthage/Carthage) is an alternative dependency management system for Cocoa applications that has gained some traction recently. It prides itself on being a simple dependency manager that avoids the complexity of CocoaPods. To keep this simplicity, it doesn't automatically integrate the dependencies into your project (you must do that yourself). It also only supports iOS8 and above. 

## References
* https://cocoapods.org
* https://github.com/Carthage/Carthage
* http://www.raywenderlich.com/97014/use-cocoapods-with-swift