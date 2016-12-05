# Local Data Persistence - Using Realm

## Overview
The RealmSwift library allows you to easily persist your data model.

## Project Setup - CocoaPods
- Navigate to your project directory in Terminal
- Make sure you have the latest version of Realm
```
pod repo update 
```
- Create a Podfile
```
pod init
```
- Open your Podfile in a text editor
```
open Podfile
```
- Add the RealmSwift dependency to your Podfile right below `# Pods for [Your_App]`
```
pod 'RealmSwift'
```
- Add the following to the very bottom of your Podfile (Xcode 8)
```
post_install do |installer|
  installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
      config.build_settings['SWIFT_VERSION'] = '3.0'
    end
  end
end
```
- Save and close the Podfile
- Install the RealmSwift CocoaPod
```
pod install
```
- Close your Xcode Project and open up the `.xcworkspace` file (Once you add Cocoapods, you will always use the `.xcworkspace` file instead of `.xcodeproj`)
- Clean `cmd + shift + k` and build `cmd + b` your project. This is nessesary to get your project to "see" any pods you have added. 

## Create a Class
- Import the RealmSwift module anywhere you want access to RealmSwift objects and methods
```
import RealmSwift
// If you get a "no module 'RealmSwift'" error, erase the import line and clean and build your project.
```
- Create your class as a subclass of the RealmSwift

## Saving Data

## Retrieving Data