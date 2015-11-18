Interactive Gestures can really bring your UI to life! In this guide we will explore some common use-cases for moving and transforming views based on gesture input.

## Use Case: Slide Out Tray
![Slide Out Tray Example gif](http://i.imgur.com/P4v6iHa.gif)  

Slide out trays, Hamburger menus, and any other view you want to move by sliding your finger can be configured using a UIPanGesture Recognizer.

### Step 1: Add a UIView to Serve as Your Tray
- You can [add a View in Storyboard](https://guides.codepath.com/ios/Creating-Nested-Views#step-1-add-the-parent-view) or [add a view programmatically](https://guides.codepath.com/ios/Programmatically-Creating-Views)
- If you added a view in Storyboard, create an outlet for your view. We will name the outlet `trayView`.
 
### Step 2: Add and Configure your Pan Gesture Recognizer.
- Attach a UIPanGestureRecognizer to your Tray view and create an Action/Method. We will call our method, `didPanTray`. You can [add a Gesture Recognizer in Storyboard](https://guides.codepath.com/ios/Using-Gesture-Recognizers#add-and-configure-a-gesture-recognizer-in-storyboard) or [add a Gesture Recognizer programmatically](https://guides.codepath.com/ios/Using-Gesture-Recognizers#programmatically-add-and-configure-a-gesture-recognizer)

### Step 3: Define a Variable to Hold the Initial Position of the View
Define an **Instance Variable** that can be accessed throughout the View Controller. We define these variables at the top of the ViewController Swift file, right above the `viewDidLoad` method.

```swift
var initialTrayCenter: CGPoint!
```

### Step 4: Make the Tray Draggable
![Make the Tray Draggable gif](http://i.imgur.com/sI6gV8c.gif)  

The code to make the Tray draggable will go inside our `didPanTray` method.
NOTE: If you created your Gesture Recognizer and added an Action in Storyboard, the method will be proceeded by `@IBAction`.

```swift
@IBAction func didPanTray(sender: UIPanGestureRecognizer) {
    
}
``` 

- Within the `didPanTray` method, we want to access the **translation** property of the UIPanGestureRecognizer and store it in a variable. This will tell us how far our finger has moved from the original "touch-down" point as we drag. We will also print the translation value to the console to get a feel for what it means. You should see that the translation is a **CGPoint** with values for the x and y components. 

```swift
var translation = sender.translationInView(view)
print("translation \(translation)")
```

- Create a conditional statement to check for the current gesture state during the pan: .Began, .Changed or .Ended
   - `.Began` is called once at the very beginning of each gesture recognition.
   - `.Changed` is called continuously as the user is in the process of "gesturing".
   - `.Ended` is called once at the end of the gesture.
   - HINT: You will use this conditional statement to check gesture states SO often, that is extremely helpful to [create a Code Snippet]() for quick access in the future!  

```
if sender.state == UIGestureRecognizerState.Began {
            
} else if sender.state == UIGestureRecognizerState.Changed {
            
} else if sender.state == UIGestureRecognizerState.Ended {
            
}
```

- When the gesture begans (`.Began`), store the tray's center into the trayOriginalCenter variable: 
```swift
trayOriginalCenter = trayView.center
```

- As the user pans (`.Changed`), change the `trayView.center` by the translation. Note: we ignore the x translation because we only want the tray to move up and down: 
```swift
trayView.center = CGPoint(x: trayOriginalCenter.x, y: trayOriginalCenter.y + translation.y)
```

### 5: Animate Tray to Open or Closed Positions
![Slide Out Tray Example gif](http://i.imgur.com/P4v6iHa.gif)  

When a user stops panning the Tray, we want the tray to animate to an up or down position. We will infer that if the users last gesture movement was downward, they intend to close the tray to it's down position. Conversely, if they are NOT panning down, they must be panning up, and intend to open the tray to it's up position.  
  
We can tell which way a user is panning by looking at the gesture property, **velocity**. Like translation, velocity has a value for both x and y components. If the **y component** of the velocity is a **positive** value, the user is **panning down**. If the **y component** is **negative**, the user is **panning** up.

Since we are focusing on the user's **last** gesture movement, we will check for the velocity in the `.Ended` condition of our gesture state conditional statement.

- Get the velocity: `var velocity = sender.velocityInView(view)`. [Pan Gesture Recognizer](https://guides.codepath.com/ios/Using-Gesture-Recognizers#example-2-pan-gesture-recognizer-1)
- Define Instance variables to store the tray's position when it's "up" and "down" as well as the offset amount that the tray will move down when it is in it's down position.
     
```swift
var trayDownOffset: CGFloat!
var trayUp: CGPoint!
var trayDown: CGPoint!
```
     
- Within the `viewDidLoad` method, assign values to the `trayDownOffset`, `trayUp` and `trayDown` variables . The `trayDownOffset` will dictate how much the tray moves down. 160 worked for my tray, but you will have to adjust this value to accommodate the specific size of your tray. 

```swift
trayDownOffset = 160
trayUp = trayView.center
trayDown = CGPoint(x: trayView.center.x ,y: trayView.center.y + trayDownOffset)
```
- Back in your `didPanTray` method, within the gesture state, `.Ended`, create a conditional statement to check the y component of the velocity. In the case that the tray is moving down, animate the tray position to the `trayDown` point, otherwise, animate it towards the `trayDown` point. [Animating View Properties](https://guides.codepath.com/ios/Animating-View-Properties)

```swift
if velocity.y > 0 {
   UIView.animateWithDuration(0.3, animations: { () -> Void in
   trayView.center = trayDown                 
   })
} else {
   UIView.animateWithDuration(0.3, animations: { () -> Void in
   trayView.center = trayUp                 
   })
}
```  
You can also try animating the ending tray motion with a bounce using the damping ratio and initial spring velocity. [Spring Animation](https://guides.codepath.com/ios/Animating-View-Properties#spring-animation)

## Use Case: Scaling and Rotating a View

This Use-Case will explore using multiple gesture recognizers simultaneously to scale and rotate an ImageView.  
  
![Make the Image View Rotatable|200](http://i.imgur.com/NH2U4m1.gif)

### Step 1: Add an ImageView

- You can [add an ImageView from The Media Library](https://guides.codepath.com/ios/Creating-Nested-Views#step-1-add-the-parent-view) to the Storyboard or [add a view programmatically](https://guides.codepath.com/ios/Programmatically-Creating-Views)
- Make sure that the Image View has **user interaction enabled**.

### Step 2: Add and Configure the Gesture Recognizers

- Attach a **UIPinchGestureRecognizer** and a **UIRotationGestureRecognizer** to your Image View and create an Action/Method for each one. We will call our methods, `didPinch` and `didRotate`. You can [add a Gesture Recognizer in Storyboard](https://guides.codepath.com/ios/Using-Gesture-Recognizers#add-and-configure-a-gesture-recognizer-in-storyboard) or [add a Gesture Recognizer programmatically](https://guides.codepath.com/ios/Using-Gesture-Recognizers#programmatically-add-and-configure-a-gesture-recognizer)

### Step 3: Allow for Simultaneous Gesture Recognizers

Since we will be using multiple gesture recognizers at the same time, we will need to configure our ViewController to support [Simultaneous Gesture Recognizers](https://guides.codepath.com/ios/Using-Gesture-Recognizers#using-simultaneous-gesture-recognizers).

### Step 4: Make the Image View Scalable 

![Make the Image View Scalable gif|200](http://i.imgur.com/AkacSCN.gif)  
  
Within the `didPinch` method... 
- Access the **scale** parameter of the Pinch Gesture Recognizer and store it in a constant.
- Access the view that was pinched and store it in a constant.
   - `sender.view` returns a generic UIView so we need to specify that we are working with a UIImageView using `as! UIImageView`.
- Store the previous transform state of the imageView in a constant.
- Modify the scale component of the imageView's transform property.
   - NOTE: We use `CGAffineTransformScale` instead of `CGAffineTransformMakeScale`. This is because `CGAffineTransformScale` allows us to add to the previous transform state as an argument, `previousTransfrom` whereas `CGAffineTransformMakeScale` will overwrite it completely. [Combining Transforms](https://guides.codepath.com/ios/Using-View-Transforms#combining-transforms)
- Set the scale of the UIPinchGestureRecognizer back to 1.
   - Resetting the scale is necessary because we are adding the scale to the `previousTransform` each time the method is called. If we didn't reset the scale, each time around the scale that was added to the `previousTransform` would be doubled and our Image View would scale out of control! But don't take my word for it, run the app without resetting the scale back to 1 and see what happens! 

```swift
@IBAction func didPinch(sender: UIPinchGestureRecognizer) {
let scale = sender.scale
let imageView = sender.view as! UIImageView
let currentTransform = imageView.transform
imageView.transform = CGAffineTransformScale(imageView.transform, scale, scale)
sender.scale = 1
}
```

### Step 5: Make the Image View Rotatable 
  
Within the `didRotate` method... 
- Access the **rotation** parameter of the Rotation Gesture Recognizer and store it in a constant.
- Access the view that was rotated and store it in a constant.
   - `sender.view` returns a generic UIView so we need to specify that we are working with a UIImageView using `as! UIImageView`.
- Store the current transform state of the imageView
- Modify the rotation component of the imageView's transform property.
   - NOTE: We use `CGAffineTransformRotate` instead of `CGAffineTransformMakeRotate` for the same reasons we chose our scale transform method. [Combining Transforms](https://guides.codepath.com/ios/Using-View-Transforms#combining-transforms)
- Set the rotation of the UIRotationGestureRecognizer back to 0.  
  
```swift    
@IBAction func didRotate(sender: UIRotationGestureRecognizer) {
   let rotation = sender.rotation
   let imageView = sender.view as! UIImageView
   let previousTransform = imageView.transform
   imageView.transform = CGAffineTransformRotate(previousTransform, rotation)
   sender.rotation = 0
}
```