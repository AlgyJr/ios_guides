## Overview

This is a quickstart guide for using the taking a photo or picking an image from the camera roll using the stock camera and camera roll. In both cases, we'll modally present the `UIImagePickerController` class which has a delegate. The delegate has a method which is called after a user takes/picks a picture.

## Taking a Picture

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
vc.sourceType = UIImagePickerControllerSourceType.camera

self.present(vc, animated: true, completion: nil)
```

**NOTE:** The Xcode simulator does not support taking pictures, so you may want to check that the camera is indeed supported on the device before trying to present it.

```
if UIImagePickerController.isSourceTypeAvailable(.camera) {
   print("Camera is available 📸")
   vc.sourceType = .camera
} else {
   print("Camera 🚫 available so we will use photo library instead")
   vc.sourceType = .photoLibrary
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