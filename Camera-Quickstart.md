## Overview

This is a quickstart guide for using the taking a photo or picking an image from the camera roll using the stock camera and camera roll. In both cases, we'll modally present the `UIImagePickerController` class which has a delegate. The delegate has a method which is called after a user takes/picks a picture.

## Taking a Picture

### Step 1: Instantiate a UIImagePickerController

*Swift*
```
var vc = UIImagePickerController()
vc.delegate = self
vc.allowsEditing = true
vc.sourceType = UIImagePickerControllerSourceType.Camera

self.presentViewController(vc, animated: true, completion: nil)
```

*Objective-C*
```

```

### Step 2: Implement the delegate

In the class interface, declare that it implements two protocols: `UIImagePickerControllerDelegate` and `UINavigationControllerDelegate`.

*Swift*
```
func imagePickerController(picker: UIImagePickerController,
    didFinishPickingMediaWithInfo info: [NSObject : AnyObject]) {
    var originalImage = info[UIImagePickerControllerOriginalImage] as UIImage
    var editedImage = info[UIImagePickerControllerEditedImage] as UIImage
}
```

When the user finishes taking the picture, `UIImagePickerController` returns a dictionary that contains the image and some other meta data. The full set of keys are listed [here](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImagePickerControllerDelegate_Protocol/index.html#//apple_ref/doc/constant_group/Editing_Information_Keys).

## Picking a Picture from the Camera Roll