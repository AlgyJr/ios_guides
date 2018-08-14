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
var captureSession: AVCaptureSession!
var stillImageOutput: AVCapturePhotoOutput!
var videoPreviewLayer: AVCaptureVideoPreviewLayer!
``` 
```objc
@interface CameraViewController ()
@property (nonatomic) AVCaptureSession *session;
@property (nonatomic) AVCapturePhotoOutput *stillImageOutput;
@property (nonatomic) AVCaptureVideoPreviewLayer *videoPreviewLayer;
@end
```

### Step 5: Create a viewWillAppear Method
The bulk of the camera setup will happen in the `viewDidAppear`.
- NOTE: Make sure to call `super.viewDidAppear(animated)` also.

```swift
override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)
    // Setup your camera here...
}
```
```objc
- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];    
    // Setup your camera here...
}
```

### Step 6: Setup Session
The session will coordinate the input and output data from the devices camera.  
Still in `viewDidAppear`
- Create a new session
- Configure the session for high resolution still photo capture. We'll use a convenient preset to that. 

```swift
captureSession = AVCaptureSession()
captureSession.sessionPreset = .medium
```
```objc
self.session = [AVCaptureSession new];
self.session.sessionPreset = AVCaptureSessionPresetPhoto;
```
- NOTE: If you plan to upload your photo to Parse, you will likely need to change your preset to `AVCaptureSession.Preset.High` or `AVCaptureSession.Preset.medium` to keep the size under the 10mb Parse max.

### Step 7: Select Input Device
In this example, we will be using the **rear camera**. The front camera and microphone are additional input devices at your disposal.  Printing debug comment incase the fetching the rear camera fails.
Still in `viewDidAppear`
```swift
guard let backCamera = AVCaptureDevice.default(for: AVMediaType.video)
    else {
        print("Unable to access back camera!")
        return
}
```
```objc
AVCaptureDevice *backCamera = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
if (!backCamera) {
    NSLog(@"Unable to access back camera!");
    return;
}
```
### Step 8: Prepare the Input
We now need to make an **AVCaptureDeviceInput**. The AVCaptureDeviceInput will serve as the "middle man" to attach the input device, `backCamera` to the session.
- We will make a new **AVCaptureDeviceInput** and attempt to associate it with our `backCamera` input device. 
- There is a chance that the input device might not be available, so we will set up a `try` `catch` to handle any potential **errors** we might encounter.  In Objective C, errors will be using the traditional NSError pattern.
Still in `viewDidAppear`

```swift
do {
    let input = try AVCaptureDeviceInput(device: backCamera)
    //Step 9
}
catch let error  {
    print("Error Unable to initialize back camera:  \(error.localizedDescription)")
}
```
```objc
NSError *error;
AVCaptureDeviceInput *input = [AVCaptureDeviceInput deviceInputWithDevice:backCamera
                                                                    error:&error];
if (!error) {
    //Step 9
}
else {
    NSLog(@"Error Unable to initialize back camera: %@", error.localizedDescription);
}
```

### Step 9: Configure the Output
Just like we created an AVCaptureDeviceInput to be the "middle man" to attach the input device, we will use **AVCapturePhotoOutput** to help us attach the **output** to the session.
- Create a new **AVCapturePhotoOutput** object.

```swift
stillImageOutput = AVCapturePhotoOutput()
```
```objc
self.stillImageOutput = [AVCapturePhotoOutput new];
```

### Step 10: Attach the Input and Output
If there are no errors from our last step and the session is able to accept input and output, the go ahead and **add input** **add output** to the **Session**.

```swift
stillImageOutput = AVCapturePhotoOutput()

if captureSession.canAddInput(input) && captureSession.canAddOutput(stillImageOutput) {
    captureSession.addInput(input)
    captureSession.addOutput(stillImageOutput)
    setupLivePreview()
}
```
```objc
self.stillImageOutput = [AVCapturePhotoOutput new];

if ([self.capturesSession canAddInput:input] && [self.capturesSession canAddOutput:self.stillImageOutput]) {
    
    [self.capturesSession addInput:input];
    [self.capturesSession addOutput:self.stillImageOutput];
    [self setupLivePreview];
}
``` 

### Step 11: Configure the Live Preview
Now that the input and output are all hooked up with our session, we just need to get our Live Preview going so we can actually display what the camera sees on the screen in our UIView, `previewView`.
- Create an **AVCaptureVideoPreviewLayer** and associate it with our session.
- Configure the Layer to resize while maintaining it's original aspect.
- Fix the orientation to portrait
- Add the preview layer as a sublayer of our `previewView`
- Finally, **start the session!**

```swift
func setupLivePreview() {
    
    videoPreviewLayer = AVCaptureVideoPreviewLayer(session: captureSession)
    
    videoPreviewLayer.videoGravity = .resizeAspect
    videoPreviewLayer.connection?.videoOrientation = .portrait
    previewView.layer.addSublayer(videoPreviewLayer)
    
    //Step12
}
```
```objc
- (void)setupLivePreview {
    
    self.videoPreviewLayer = [AVCaptureVideoPreviewLayer layerWithSession:self.capturesSession];
    if (self.videoPreviewLayer) {
        
        self.videoPreviewLayer.videoGravity = AVLayerVideoGravityResizeAspect;
        self.videoPreviewLayer.connection.videoOrientation = AVCaptureVideoOrientationPortrait;
        [self.previewView.layer addSublayer:self.videoPreviewLayer];
        
        //Step12
    }
}
```
### Step 12: Start the **Session** on the background thread
We need to call `-startRunning` on the session to start the live view.  However `-startRunning` is a blocking method which means it will block the UI if it's running on the main thread.  If the session takes a while to start, users would want the UI to be responsive and cancel out of the camera view.
```swift
DispatchQueue.global(qos: .userInitiated).async { //[weak self] in
    self.captureSession.startRunning()
    //Step 13
}
```
```objc
dispatch_queue_t globalQueue =  dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0);
dispatch_async(globalQueue, ^{
    [self.capturesSession startRunning];
    //Step 13
});
```
### Step 13: Size the Preview Layer to fit the Preview View
Once the live view starts let's set the Preview layer to fit, but we must return to the main thread to do so!

```swift
DispatchQueue.main.async {
    self.videoPreviewLayer.frame = self.previewView.bounds
}
```
```objc
dispatch_async(dispatch_get_main_queue(), ^{
    self.videoPreviewLayer.frame = self.previewView.bounds;
});
```
### Step 14: Taking the picture
Let's create an IBAction of the `Take photo Button` and capture a JPEG by calling our instance of `AVCapturePhotoOutput` or `stillImageOut` the method `func capturePhoto(with:, delegate:)` or `-capturePhotoWithSettings:delegate:`.  This method requires us to provide it with a setting and a deleget to deliver the capturedPhoto to.  This delegate will be this ViewController so we also need to conform to the protocol `AVCapturePhotoCaptureDelegate`
```swift
class CameraViewControllerSwift: UIViewController, AVCapturePhotoCaptureDelegate {
    ....
    @IBAction func didTakePhoto(_ sender: Any) {
        
        let settings = AVCapturePhotoSettings(format: [AVVideoCodecKey: AVVideoCodecType.jpeg])
        stillImageOutput.capturePhoto(with: settings, delegate: self)
    }
}
```
```objc
//CameraViewController.h
@interface CameraViewController : UIViewController <AVCapturePhotoCaptureDelegate>

//CameraViewController.m
- (IBAction)didTakePhoto:(id)sender {
    
    AVCapturePhotoSettings *settings = [AVCapturePhotoSettings photoSettingsWithFormat:@{AVVideoCodecKey: AVVideoCodecTypeJPEG}];

    [self.stillImageOutput capturePhotoWithSettings:settings delegate:self];
}
```
### Step 15: Process the captured photo!
The `AVCapturePhotoOutput` will deliver the captured photo to the assigned delegate which is our current ViewController by a delegate method called `photoOutput(_ output: AVCapturePhotoOutput, didFinishProcessingPhoto photo: AVCapturePhoto, error: Error?)`.  The photo is delivered to us as an `AVCapturePhoto` which is easy to transform into `Data/NSData` and than into UIImage.
```swift
func photoOutput(_ output: AVCapturePhotoOutput, didFinishProcessingPhoto photo: AVCapturePhoto, error: Error?) {
    
    guard let imageData = photo.fileDataRepresentation()
        else { return }
    
    let image = UIImage(data: imageData)
    captureImageView.image = image
}
```
```objc
- (void)captureOutput:(AVCapturePhotoOutput *)output didFinishProcessingPhoto:(AVCapturePhoto *)photo error:(nullable NSError *)error {
    
    NSData *imageData = photo.fileDataRepresentation;
    if (imageData) {
        UIImage *image = [UIImage imageWithData:imageData];
        // Add the image to captureImageView here...
        self.captureImageView.image = image;
    }
}
```



### Step 16: Clean up when the user leaves!
Let's not forget to stop the session when we leave the camera view!

```swift
override func viewWillDisappear(_ animated: Bool) {
    super.viewWillDisappear(animated)
    self.captureSession.stopRunning()
}
```
```objc
- (void)viewWillDisappear:(BOOL)animated {
    [super viewWillDisappear:animated];
    [self.capturesSession stopRunning];
}
```



### Step 17: Run Your App ON A REAL DEVICE!!!
NOTE: The simulator does NOT have a camera so you need to run your app on an **Actual Device** to see the magic!
- At this point, you should see a live "video" stream of your phone camera's input within your `previewView`.