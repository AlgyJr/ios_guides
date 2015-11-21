Accessing the built in [Image Picker Controller]() is a quick and easy way to get image and video capture into your app. However, when you need style and functionality that goes beyond the stock Image Picker Controller you will need to create a Custom Camera View.  
  
![Custom Camera View Demo](http://i.imgur.com/LkALEvS.gif)

### Step 1: Set Up Views In Storyboard 

![Set Up Views In Storyboard gif|200](http://i.imgur.com/KhbVi00.png) ![Outline Document gif|60](http://i.imgur.com/6CSyrY7.png)  
  
Add the following view elements to the ViewController in Storyboard:
- **UIView** This will serve as the "view finder" of your camera.
- **UIImageView** This will hold the captured still image after you take a picture.
- **UIButton** This button will "take a picture".

### Step 2: Import AVFoundation
At the top of your ViewController file, **import AVFoundation**

![Import AVFoundation](http://i.imgur.com/VhEZphd.png) 

### Step 3: Create Outlets and Actions
Create **Outlets** for the UIView and UIImageView. 
- Name the UIView, `previewView`.
- Name the UIImageView, `captureImageView`.

Create an **Action** for the UIButton.
- Name the method, `didTakePhoto`.

### Step 4: Define Instance Variables
Above the `viewDidLoad` method, where you create variables you want to be accessible anywhere in the ViewController file, create the following **Instance Variables**.

```swift
var session: AVCaptureSession?
var stillImageOutput: AVCaptureStillImageOutput?
var videoPreviewLayer: AVCaptureVideoPreviewLayer?
``` 

### Step 5: Create a viewWillAppear Method
The bulk of the camera setup will happen in the `viewDidLoad`.
- NOTE: Make sure to call `super.viewWillAppear(animated)` also.

```swift
override func viewWillAppear(animated: Bool) {
   super.viewWillAppear(animated)
   // Setup your camera here...
}
```

### Step 6: Setup Session
The session will coordinate the input and output data from the devices camera. 
- Create a new session
- Configure the session for high resolution still photo capture. We'll use a convenient preset to that. 

```swift
session = AVCaptureSession()
session!.sessionPreset = AVCaptureSessionPresetPhoto
```

### Step 7: Select Input Device
In this example, we will be using the **rear camera**. The front camera and microphone are additional input devices at your disposal.

```swift
let backCamera = AVCaptureDevice.defaultDeviceWithMediaType(AVMediaTypeVideo)
```

### Step 8: Prepare the Input
We now need to make an **AVCaptureDeviceInput**. The AVCaptureDeviceInput will serve as the "middle man" to attach the input device, `backCamera` to the session.
- We will make a new **AVCaptureDeviceInput** and attempt to associate it with our `backCamera` input device. 
- There is a chance that the input device might not be available, so we will set up a `try` `catch` to handle any potential **errors** we might encounter.

```swift
var error: NSError?
var input: AVCaptureDeviceInput!
do {
   input = try AVCaptureDeviceInput(device: backCamera)
} catch let error1 as NSError {
   error = error1
   input = nil
   print(error!.localizedDescription)
}
```

### Step 9: Attach the Input
If there are no errors from our last step and the session is able to accept input, the go ahead and **add input** to the **Session**.

```swift
if error == nil && session!.canAddInput(input) {
   session!.addInput(input)
   ...
   // The remainder of the session setup will go here...
}
``` 

### Step 10: Configure the Output
Just like we created an AVCaptureDeviceInput to be the "middle man" to attach the input device, we will use **AVCaptureStillImageOutput** to help us attach the **output** to the session.
- Create a new **AVCaptureStillImageOutput** object.
- Set the output data setting to use JPEG format.

```swift
stillImageOutput = AVCaptureStillImageOutput()
stillImageOutput?.outputSettings = [AVVideoCodecKey: AVVideoCodecJPEG]
```

### Step 11: Attach the Output
If the session is able to accept our output, then we will **attach the output to the session**.