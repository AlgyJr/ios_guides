Accessing the built in [Image Picker Controller]() is a quick and easy way to get image and video capture into your app. However, when you need style and functionality that goes beyond the stock Image Picker Controller you will need to create a Custom Camera View.  
  
![Custom Camera View Demo](https://i.imgur.com/LkALEvS.gif)

## Live Camera Preview
### Step 1: Set Up Views In Storyboard 

![Set Up Views In Storyboard gif|200](https://i.imgur.com/KhbVi00.png) ![Outline Document gif|60](https://i.imgur.com/6CSyrY7.png)  
  
Add the following view elements to the ViewController in Storyboard:
- **UIView** This will serve as the "view finder" of your camera.
- **UIImageView** This will hold the captured still image after you take a picture.
- **UIButton** This button will "take a picture".

### Step 2: Import AVFoundation
At the top of your ViewController file, **import AVFoundation**

![Import AVFoundation](https://i.imgur.com/VhEZphd.png) 

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
```objc
@interface CameraViewController ()
@property (nonatomic) AVCaptureSession *session;
@property (nonatomic) AVCapturePhotoOutput *stillImageOutput;
@property (nonatomic) AVCaptureVideoPreviewLayer *videoPreviewLayer;
@end
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
```objc
- (void)viewDidLoad {
    [super viewDidLoad];
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
```objc
self.session = [AVCaptureSession new];
self.session.sessionPreset = AVCaptureSessionPresetPhoto;
```
- NOTE: If you plan to upload your photo to Parse, you will likely need to change your preset to `AVCaptureSessionPresetHigh` or `AVCaptureSessionPresetMedium` to keep the size under the 10mb Parse max.

### Step 7: Select Input Device
In this example, we will be using the **rear camera**. The front camera and microphone are additional input devices at your disposal.

```swift
let backCamera = AVCaptureDevice.defaultDeviceWithMediaType(AVMediaTypeVideo)
```
```objc
AVCaptureDevice *backCamera = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
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
```objc
NSError *error;
AVCaptureDeviceInput *input = [AVCaptureDeviceInput deviceInputWithDevice:backCamera
                                                                    error:&error];
if (error) {
    NSLog(@"%@", error.localizedDescription);
}
```
### Step 9: Attach the Input
If there are no errors from our last step and the session is able to accept input, the go ahead and **add input** to the **Session**.

```swift
if error == nil && session!.canAddInput(input) {
  session!.addInput(input)
  // ...
  // The remainder of the session setup will go here...
}
```
```objc
// Continue from above
else if (self.session && [self.session canAddInput:input]) {
    [self.session addInput:input];
  // The remainder of the session setup will go here...
}
``` 

### Step 10: Configure the Output
Just like we created an AVCaptureDeviceInput to be the "middle man" to attach the input device, we will use **AVCaptureStillImageOutput** to help us attach the **output** to the session.
- Create a new **AVCaptureStillImageOutput** object.
- Set the output data setting to use JPEG format.

```swift
//Need to update to AVCapturePhotoOutput
//stillImageOutput = AVCaptureStillImageOutput()
//stillImageOutput?.outputSettings = [AVVideoCodecKey: AVVideoCodecJPEG]
```
```objc
self.stillImageOutput = [AVCapturePhotoOutput new];
```

### Step 11: Attach the Output
If the session is able to accept our output, then we will **attach the output to the session**.

```swift
if session!.canAddOutput(stillImageOutput) {
  session!.addOutput(stillImageOutput)
  // ...
  // Configure the Live Preview here... 
}
```
```objc
if ([self.session canAddOutput:self.stillImageOutput]) {
    [self.session addOutput:self.stillImageOutput];
    
    //Configure the Live Preiview here
    
}

//Also let the ViewController conform to `AVCapturePhotoCaptureDelegate`
#import <UIKit/UIKit.h>
@import AVKit;
@interface CameraViewController : UIViewController <AVCapturePhotoCaptureDelegate>
```

### Step 12: Configure the Live Preview
Now that the input and output are all hooked up with our session, we just need to get our Live Preview going so we can actually display what the camera sees on the screen in our UIView, `previewView`.
- Create an **AVCaptureVideoPreviewLayer** and associate it with our session.
- Configure the Layer to resize while maintaining it's original aspect.
- Fix the orientation to portrait
- Add the preview layer as a sublayer of our `previewView`
- Finally, **start the session!**

```swift
videoPreviewLayer = AVCaptureVideoPreviewLayer(session: session)
videoPreviewLayer!.videoGravity = AVLayerVideoGravityResizeAspect
videoPreviewLayer!.connection?.videoOrientation = AVCaptureVideoOrientation.Portrait
previewView.layer.addSublayer(videoPreviewLayer!)
session!.startRunning()
```
```objc
self.videoPreviewLayer = [AVCaptureVideoPreviewLayer layerWithSession:self.session];
if (self.videoPreviewLayer) {
    self.videoPreviewLayer.videoGravity = AVLayerVideoGravityResizeAspect;
    self.videoPreviewLayer.connection.videoOrientation = AVCaptureVideoOrientationPortrait;
    [self.previewView.layer addSublayer:self.videoPreviewLayer];
    [self.session startRunning];
}
```

### Step 13: Size the Preview Layer to fit the Preview View
- Create a `viewDidAppear` method. just like with the `viewWillAppear` method, we will want to call the `super.` of the `viewDidAppear` method.
- Within the `viewDidAppear` method, set the size and origin of the Preview Layer to fit inside the Preview View. We will do this using the **bounds** property.

```swift
override func viewDidAppear(animated: Bool) {
   super.viewDidAppear(animated)
   videoPreviewLayer!.frame = previewView.bounds
}
```
```objc
- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];
    self.videoPreviewLayer.frame = self.previewView.bounds;
}
```

### Step 14: Run Your App ON A REAL DEVICE!!!
NOTE: The simulator does NOT have a camera so you need to run your app on an **Actual Device** to see the magic!
- At this point, you should see a live "video" stream of your phone camera's input within your `previewView`.

## Snap a Photo
### Step 1: Get the Connection
- Setup capture setting for this particular capture.

```objc
- (IBAction)didTakePhoto:(id)sender {
    AVCapturePhotoSettings *settings = [AVCapturePhotoSettings photoSettingsWithFormat:@{AVVideoCodecKey: AVVideoCodecTypeJPEG}];
//Capture Photo
}
```

### Step 2: Capture the Photo
- Call the `-capturePhotoWithSettings:delegate:` function on the `stillImageOutput`.

```objc
[self.stillImageOutput capturePhotoWithSettings:settings delegate:self]; 
//Don't for get to conform to AVCapturePhotoCaptureDelegate
```
### Step 3: Capture the Image Data
- The AVCapturePhotoCaptureDelegate methods provide us with an instance of AVCapturePhoto object which has all we need to get a **UIImage** that we can insert into our `captureImageView` and easily use elsewhere in our app.

```objc
- (void)captureOutput:(AVCapturePhotoOutput *)output didFinishProcessingPhoto:(AVCapturePhoto *)photo error:(nullable NSError *)error {
    
    NSData *imageData = photo.fileDataRepresentation;
    UIImage *image = [UIImage imageWithData:imageData];
    // Add the image to captureImageView here...
}
```


### Step 1: Get the Connection
- Get the connection from the `stillImageOutput`.

```swift
if let videoConnection = stillImageOutput!.connectionWithMediaType(AVMediaTypeVideo) {
  // ...
  // Code for photo capture goes here...
}
```

### Step 2: Capture the Photo
- Call the `captureStillImageAsynchronouslyFromConnection` function on the `stillImageOutput`.
- The `sampleBuffer` represents the data that is captured.

```swift
stillImageOutput?.captureStillImageAsynchronouslyFromConnection(videoConnection, completionHandler: { (sampleBuffer, error) -> Void in
  // ...
  // Process the image data (sampleBuffer) here to get an image file we can put in our captureImageView
})
```

### Step 3: Process the Image Data
- We will need to to take a few steps to process the image data found in `sampleBuffer` in order to end up with a **UIImage** that we can insert into our `captureImageView` and easily use elsewhere in our app.

```swift
if sampleBuffer != nil {
  let imageData = AVCaptureStillImageOutput.jpegStillImageNSDataRepresentation(sampleBuffer)
  let dataProvider = CGDataProviderCreateWithCFData(imageData)
  let cgImageRef = CGImageCreateWithJPEGDataProvider(dataProvider, nil, true, CGColorRenderingIntent.RenderingIntentDefault)
  let image = UIImage(CGImage: cgImageRef!, scale: 1.0, orientation: UIImageOrientation.Right)
  // ...
  // Add the image to captureImageView here...
}
```

**ATTENTION:** pay attention here. We are generating a rotated `UIImage` instance, but the backed `CGImage` will still be rotated in the wrong way.

### Step 4: Add the Image to the ImageView
- Finally, add the image to `captureImageView`.

```swift
self.captureImageView.image = image
```
```objc
self.captureImageView.image = image;
```
### Step 5: Run Your App ON A REAL DEVICE!!!
NOTE: The simulator does NOT have a camera so you need to run your app on an **Actual Device** to see the magic!
- At this point, you should see a live "video" stream of your phone camera's input within your `previewView` and the ability to "Snap" a photo and see the still image within your `captureImageView`.
- NOTE: We are just adding the image to the `captureImageView` to illustrate the technique of capturing a still image. Once you have the still image you can do all kinds of cool things, like save it into your photo library, or upload it to Parse for use elsewhere in your app.
