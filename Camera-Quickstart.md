## Overview

This is a quickstart guide for using the taking a photo or picking an image from the camera roll using the stock camera and camera roll. In both cases, we'll modally present the `UIImagePickerController` class which has a delegate. The delegate has a method which is called after a user takes/picks a picture.

## Permissions
As of iOS 10, the following camera and photo library usage descriptions are required in the `Info.plist`. The description you enter will be shown to the user when prompted to allow permissions to the camera or photo library.

### Step 1: Open `Info.plist`
Select the `Info.plist`file in the documents outline.  There are two ways to view permissions set in your `Info.plist` file, and the names of the permissions vary slightly depending on whether you view the file as source code or as a property list.  You can view it in XML by right-clicking the file name, going to "Open As" and selecting "Source Code."    Alternatively, you can right-click the file name, go to "Open As" and select "Property List".  XCode defaults to opening the `Info.plist` file in the Property List view.   

### Step 2: Add Permissions

If you opened `Info.plist` as source code, add the XML lines below inside the `<dict>`.
```xml
<key>NSPhotoLibraryUsageDescription</key>               <!-- added this for photo library permission -->
<string>Need library access to upload images</string> <!-- added this for photo library permission -->
<key>NSCameraUsageDescription</key> <!-- added this for camera permission -->
<string>Need camera access to take pictures</string> <!-- added this for camera permission -->
```

If you opened `Info.plist` as a property list, add the following two keys:

1. `Privacy - Photo Library Usage Description` with the value `Need library access to upload images`
2. `Privacy - Camera Usage Description` with the value `Need camera access to take pictures`.

**Creating a Key**
1. Hover over the top cell that says `Information Property List`
2. Click the grey plus button that appears.  A new cell will now appear nested under the `Information Property List`
3. In thew new cell, start typing the key for your new property. E.g. "Privacy - Photo Library Usage Description" (without the quotes).  XCode will search for properties starting with the text you have entered and you can select yours when you see it.
4. In the `Value` cell, type the description you want shown to the user when prompted to allow the permission.  E.g. "Need library access to upload images"

Note that for the two permissions we are adding for the camera and library access, the `Type` value should be left as `String`.  You can drag permissions to any place inside their parent.  For example you could drag the camera and photo library permissions to be next to each other.

<img src="https://i.imgur.com/9EoXRMK.gif" width="800"/><br>

## Taking a Picture

### Step 1: Declare that your class implements the required protocols
- Add `UIImagePickerControllerDelegate` and `UINavigationControllerDelegate` when defining your class or as an [extension](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Extensions.html) of your view controller class.

    ```swift
    class PhotoMapViewController: UIViewController, UIImagePickerControllerDelegate, UINavigationControllerDelegate {
    ...
    }
    ```
```objc
#import <UIKit/UIKit.h>
@interface FeedViewController : UIViewController <UITableViewDelegate, UITableViewDataSource, UIImagePickerControllerDelegate, UINavigationControllerDelegate>
```

### Step 2: Instantiate a UIImagePickerController

```swift
let vc = UIImagePickerController()
vc.delegate = self
vc.allowsEditing = true
vc.sourceType = UIImagePickerControllerSourceType.camera

self.present(vc, animated: true, completion: nil)
```
```objc
UIImagePickerController *imagePickerVC = [UIImagePickerController new];
imagePickerVC.delegate = self;
imagePickerVC.allowsEditing = YES;
imagePickerVC.sourceType = UIImagePickerControllerSourceTypeCamera;

[self presentViewController:imagePickerVC animated:YES completion:nil];
```

**NOTE:** The Xcode simulator does not support taking pictures, so you may want to check that the camera is indeed supported on the device before trying to present it.

```swift
if UIImagePickerController.isSourceTypeAvailable(.camera) {
   print("Camera is available 📸")
   vc.sourceType = .camera
} else {
   print("Camera 🚫 available so we will use photo library instead")
   vc.sourceType = .photoLibrary
}
```
```objc
if ([UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypeCamera]) {
    imagePickerVC.sourceType = UIImagePickerControllerSourceTypeCamera;
}
else {
    NSLog(@"Camera 🚫 available so we will use photo library instead");
    imagePickerVC.sourceType = UIImagePickerControllerSourceTypePhotoLibrary;
}
```

### Step 3: Implement the delegate method

```swift
func imagePickerController(_ picker: UIImagePickerController, 
didFinishPickingMediaWithInfo info: [String : Any]) {
    // Get the image captured by the UIImagePickerController
    let originalImage = info[UIImagePickerControllerOriginalImage] as! UIImage
    let editedImage = info[UIImagePickerControllerEditedImage] as! UIImage

    // Do something with the images (based on your use case)

    // Dismiss UIImagePickerController to go back to your original view controller
    dismiss(animated: true, completion: nil)
}
```

When the user finishes taking the picture, `UIImagePickerController` returns a dictionary that contains the image and some other meta data. The full set of keys are listed [here](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImagePickerControllerDelegate_Protocol/index.html#//apple_ref/doc/constant_group/Editing_Information_Keys).

## Picking a Picture from the Camera Roll

### Step 1: Declare that your class implements the required protocols
- Add `UIImagePickerControllerDelegate` and `UINavigationControllerDelegate` when defining your class or as an [extension](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Extensions.html) of your view controller class.

    ```swift
    class PhotoMapViewController: UIViewController, UIImagePickerControllerDelegate, UINavigationControllerDelegate {
    ...
    }
    ```

### Step 2: Instantiate a UIImagePickerController

```swift
let vc = UIImagePickerController()
vc.delegate = self
vc.allowsEditing = true
vc.sourceType = UIImagePickerControllerSourceType.photoLibrary

self.present(vc, animated: true, completion: nil)
```

### Step 3: Implement the delegate method

```swift
func imagePickerController(_ picker: UIImagePickerController, 
didFinishPickingMediaWithInfo info: [String : Any]) {
    // Get the image captured by the UIImagePickerController
    let originalImage = info[UIImagePickerControllerOriginalImage] as! UIImage
    let editedImage = info[UIImagePickerControllerEditedImage] as! UIImage

    // Do something with the images (based on your use case)

    // Dismiss UIImagePickerController to go back to your original view controller
    dismiss(animated: true, completion: nil)
}
```