# Local Data Persistence - Using Realm

## Overview

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

## Create a Class

## Saving Data

## Retrieving Data