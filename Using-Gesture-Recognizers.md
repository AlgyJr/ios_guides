Gesture recognizers are a powerful and easy to use tool for handling user gestures like tap or pinch and performing actions like triggering animations or view changes. This is a quick guide for the steps in setting up gesture recognizers.

### Gesture Recognizer Options

There are many builtin gesture recognizers in iOS, and you can also create your own. Choose one from the following list.

- **UITapGestureRecognizer:** Can be configured with the number of required taps to handle single, double, or more taps.

- **UILongPressGestureRecognizer:** Can be configured with the required delay to be considered a long press. Defaults to 0.5 s.

- **UIPanGestureRecognizer**

- **UIPinchGestureRecognizer:** Used for pinch in and pinch out, this gesture recognizer continuously outputs to a `scale` property as the user moves their finger.

- **UIRotationGestureRecognizer:** Continuously outputs to `rotation` in radians.

- **UIScreenEdgePanGestureRecognizer:** Can be configured with which edge to detect pans from. Only detects pan movements that begin from the specified edge.

## Add and Configure a Gesture Recognizer in Storyboard

### Step 1: Choose a Gesture Recognizer
Choose a Gesture Recognizer from the Object Library based on the functionality your app needs.  
![Choose a Gesture Recognizer from Object Library gif|200](https://i.imgur.com/evQqzAL.gif)

### Step 2: Drag and Drop a Gesture Recognizer into your View
Drag a Gesture recognizer from the Object Library and drop it onto the View you want the Gesture to be attached to. 
- NOTE: You can see which view the Gesture Recognizer is attached to by ctrl + Clicking on the Gesture Recognizer in the Document Outline.<br>

   ![Drag and Drop a Gesture Recognizer into your View](https://i.imgur.com/Yv8WOcP.gif)<br>

### Step 3: Create an Action for your Gesture Recognizer
Ctrl-drag from your Gesture Recognizer to the ViewController swift file to create an action. It's often easier to drag from the Gesture Recognizer in the Document Outline. 
- NOTES: 
   1. Be sure to change the type from **AnyObject** to the specific Gesture Recognizer you are using. i.e. UIPanGestureRecognizer, etc.. 
   1. Make sure the view you attached the gesture to has **User Interaction Enabled**  <img src="https://i.imgur.com/fuZIcIP.png" height="30" /><br>

   ![Create an Action for your Gesture Recognizer](https://i.imgur.com/ddu28U5.gif)<br>

## Programmatically Add and Configure a Gesture Recognizer

### Step 1: Implement the event handler method

When the gesture recognizer detects the gesture, it will call the event handler method on the target. Declare the method you want called by the gesture recognizer.

Gesture recognizers call the same selector as it transitions through various states. For example, a pan gesture recognizer calls the selector when the user first touches down on the view, and then it calls the selector repeatedly as the user drags their finger across the screen, and finally it calls the selector one last time when the user lifts their finger off the screen.

#### Example: Tap gesture recognizer

```swift
func didTap(sender: UITapGestureRecognizer) {
   let location = sender.location(in: view)
   // User tapped at the point above. Do something with that if you want.
}
```
```objc
- (IBAction)didTap:(UITapGestureRecognizer *)sender {    
    CGPoint location = [sender locationInView:self.view];  
   // User tapped at the point above. Do something with that if you want.  
}
```

#### Example: Pan gesture recognizer

```swift
func didPan(sender: UIPanGestureRecognizer) {
   let location = sender.location(in: view)
   let velocity = sender.velocity(in: view)
   let translation = sender.translation(in: view)
    
   if sender.state == .began {
      print("Gesture began")
   } else if sender.state == .changed {
      print("Gesture is changing")
   } else if sender.state == .ended {
      print("Gesture ended")
   }
}
```

#### Example: Pinch gesture recognizer

```swift
func didPinch(sender: UIPinchGestureRecognizer) {
   // get the scale value from the pinch gesture recognizer
   let scale = sender.scale 
}
```

#### Example: Screen edge pan gesture recognizer

```swift
func didScreenEdgePan(sender: UIScreenEdgePanGestureRecognizer) {
   // Do something when the user does a screen edge pan.
}
```
### Step 2: Instantiate the gesture recognizer

It is common to create the gesture recognizers in the `viewDidLoad()` method, as shown below. After you create the gesture recognizer, attach it to the view you want to detect gestures on.

#### Example: Tap gesture recognizer

```swift
   // The didTap: method will be defined in Step 3 below.
   let tapGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(didTap(sender:)))

   // Optionally set the number of required taps, e.g., 2 for a double click
   tapGestureRecognizer.numberOfTapsRequired = 2

   // Attach it to a view of your choice. If it's a UIImageView, remember to enable user interaction
   yourView.isUserInteractionEnabled = true
   yourView.addGestureRecognizer(tapGestureRecognizer)
```

#### Example: Pan gesture recognizer

```swift
   // Here we use the method didPan(sender:), which we defined in the previous step, as the action.
   let panGestureRecognizer = UIPanGestureRecognizer(target: self, action: #selector(didPan(sender:)))

   // Attach it to a view of your choice. If it's a UIImageView, remember to enable user interaction
   yourView.isUserInteractionEnabled = true
   yourView.addGestureRecognizer(panGestureRecognizer)
```

#### Example: Screen edge pan gesture recognizer

```swift
// Instantiate and initialize the screen edge pan gesture recognizer
let screenEdgePanGestureRecognizer = UIScreenEdgePanGestureRecognizer(target: self, action: #selector(didScreenEdgePan(sender:)))

// Configure the screen edges you want to detect.
screenEdgePanGestureRecognizer.edges = UIRectEdge.left

// Attach the screen edge pan gesture recognizer to some view.
yourView.isUserInteractionEnabled = true
yourView.addGestureRecognizer(screenEdgePanGestureRecognizer)
```

### Common properties to access from each gesture recognizer
#### Tap Gesture Recognizer

```swift
let location = sender.location(in: view)
``` 

#### Pan Gesture Recognizer

```swift
let location = sender.location(in: view)
let translation = sender.translation(in: view)
let velocity = sender.velocity(in: view)
```

#### Pinch Gesture Recognizer

```swift
var scale = sender.scale
var velocity = sender.velocity
```
#### Rotation Gesture Recognizer
   
```swift
var rotation = sender.rotation 
var velocity = sender.velocity
```

## Other Gesture Recognizer Tasks

Below are some common tasks that you might have with gesture recognizers.

### Using Simultaneous Gesture Recognizers

By default, it won't work if you add multiple gesture recognizers to the same view. In order to do that, you have to specifically enable it.

#### Step 1: Set the Gesture Recognizer delegate

For example, if you want to use a pinch and rotate gesture recognizer simultaneously, choose either the pinch or the rotate gesture recognizer (it doesn't matter which one). If you created the gesture recognizer in Interface Builder, create an IBOutlet for it by Ctrl-dragging from the nib.

```swift
pinchGestureRecognizer.delegate = self

```

#### Step 2: Implement the Gesture Recognizer protocol

Go to the header file of the view controller and add UIGestureRecognizerDelegate to the list of protocols, as shown below.

```swift
class MainViewController : UIViewController, UIGestureRecognizerDelegate
...
@end

```

Implement the following UIGestureRecognizerDelegate method in your Swift file.

```swift
func gestureRecognizer(gestureRecognizer: UIGestureRecognizer, shouldRecognizeSimultaneouslyWithGestureRecognizer otherGestureRecognizer: UIGestureRecognizer) -> Bool {
return true
}
```

Now the pinch and scale gesture recognizers should work simultaneously.


## Use Case: Slide Out Tray
![Slide Out Tray Example gif](https://i.imgur.com/P4v6iHa.gif)  

Slide out trays, Hamburger menus, and any other view you want to move by sliding your finger can be configured using a UIPanGesture Recognizer.

The following steps describe the process of creating a **vertically** sliding Tray. If you would like to make a Hamburger style **horizontally** sliding View, use the same concepts described below, but use the x-components of the translation in place the y-components. If you want a view that can be panned in all directions, use both x and y-components of the translation.

### Step 1: Add a UIView to Serve as Your Tray
- You can [add a View in Storyboard](https://guides.codepath.com/ios/Creating-Nested-Views#step-1-add-the-parent-view) or [add a view programmatically](https://guides.codepath.com/ios/Programmatically-Creating-Views)
- If you added a view in Storyboard, create an outlet for your view. We will name the outlet `trayView`.
 
### Step 2: Add and Configure your Pan Gesture Recognizer.
- Attach a UIPanGestureRecognizer to your Tray view and create an Action/Method. We will call our method, `didPanTray`. You can [add a Gesture Recognizer in Storyboard](https://guides.codepath.com/ios/Using-Gesture-Recognizers#add-and-configure-a-gesture-recognizer-in-storyboard) or [add a Gesture Recognizer programmatically](https://guides.codepath.com/ios/Using-Gesture-Recognizers#programmatically-add-and-configure-a-gesture-recognizer)

### Step 3: Define a Variable to Hold the Initial Position of the View
Define an **Instance Variable** that can be accessed throughout the View Controller. We define these variables at the top of the ViewController Swift file, right above the `viewDidLoad` method.

```swift
var trayOriginalCenter: CGPoint!
```

### Step 4: Make the Tray Draggable
![Make the Tray Draggable gif](https://i.imgur.com/sI6gV8c.gif)  

The code to make the Tray draggable will go inside our `didPanTray` method.
NOTE: If you created your Gesture Recognizer and added an Action in Storyboard, the method will be proceeded by `@IBAction`.

```swift
@IBAction func didPanTray(sender: UIPanGestureRecognizer) {
    
}
``` 

- Within the `didPanTray` method, we want to access the **translation** property of the UIPanGestureRecognizer and store it in a variable. This will tell us how far our finger has moved from the original "touch-down" point as we drag. We will also print the translation value to the console to get a feel for what it means. You should see that the translation is a **CGPoint** with values for the x and y components. 

```swift
let translation = sender.translation(in: view)
print("translation \(translation)")
```

- Create a conditional statement to check for the current gesture state during the pan: .began, .changed or .ended
   - `.began` is called once at the very beginning of each gesture recognition.
   - `.changed` is called continuously as the user is in the process of "gesturing".
   - `.ended` is called once at the end of the gesture.
   - HINT: You will use this conditional statement to check gesture states SO often, that is extremely helpful to [create a Code Snippet](https://guides.codepath.com/ios/Using-Custom-Code-Snippets) for quick access in the future!  

```
if sender.state == .began {
            
} else if sender.state == .changed {
            
} else if sender.state == .ended {
            
}
```

- When the gesture begins (`.began`), store the tray's center into the trayOriginalCenter variable: 
```swift
trayOriginalCenter = trayView.center
```

- As the user pans (`.changed`), change the `trayView.center` by the translation. Note: we ignore the x translation because we only want the tray to move up and down: 

```swift
trayView.center = CGPoint(x: trayOriginalCenter.x, y: trayOriginalCenter.y + translation.y)
```

### 5: Animate Tray to Open or Closed Positions
![Slide Out Tray Example gif](https://i.imgur.com/P4v6iHa.gif)  

When a user stops panning the Tray, we want the tray to animate to an up or down position. We will infer that if the users last gesture movement was downward, they intend to close the tray to it's down position. Conversely, if they are NOT panning down, they must be panning up, and intend to open the tray to it's up position.  
  
We can tell which way a user is panning by looking at the gesture property, **velocity**. Like translation, velocity has a value for both x and y components. If the **y component** of the velocity is a **positive** value, the user is **panning down**. If the **y component** is **negative**, the user is **panning** up.

Since we are focusing on the user's **last** gesture movement, we will check for the velocity in the `.ended` condition of our gesture state conditional statement.

- Get the velocity: `let velocity = sender.velocity(in: view)`. [Pan Gesture Recognizer](https://guides.codepath.com/ios/Using-Gesture-Recognizers#example-2-pan-gesture-recognizer-1)
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
- Back in your `didPanTray(_:)` method, within the gesture state, `.ended`, create a conditional statement to check the y component of the velocity. In the case that the tray is moving down, animate the tray position to the `trayDown` point, otherwise, animate it towards the `trayUp` point. [Animating View Properties](https://guides.codepath.com/ios/Animating-View-Properties)

```swift
if velocity.y > 0 {
   UIView.animate(withDuration: 0.3) { 
      trayView.center = trayDown
   }   
} else {
   UIView.animate(withDuration: 0.3) {
      trayView.center = trayUp                 
   }
}
```  
You can also try animating the ending tray motion with a bounce using the damping ratio and initial spring velocity. [Spring Animation](https://guides.codepath.com/ios/Animating-View-Properties#spring-animation)

## Use Case: Scaling and Rotating a View

This Use-Case will explore using multiple gesture recognizers simultaneously to scale and rotate an ImageView.  
  
![Make the Image View Rotatable|200](https://i.imgur.com/NH2U4m1.gif)

### Step 1: Add an ImageView

- You can [add an ImageView from The Media Library](https://guides.codepath.com/ios/Creating-Nested-Views#step-1-add-the-parent-view) to the Storyboard or [add a view programmatically](https://guides.codepath.com/ios/Programmatically-Creating-Views)
- Make sure that the Image View has **user interaction enabled**.

### Step 2: Add and Configure the Gesture Recognizers

- Attach a **UIPinchGestureRecognizer** and a **UIRotationGestureRecognizer** to your Image View and create an Action/Method for each one. We will call our methods, `didPinch` and `didRotate`. You can [add a Gesture Recognizer in Storyboard](https://guides.codepath.com/ios/Using-Gesture-Recognizers#add-and-configure-a-gesture-recognizer-in-storyboard) or [add a Gesture Recognizer programmatically](https://guides.codepath.com/ios/Using-Gesture-Recognizers#programmatically-add-and-configure-a-gesture-recognizer)

### Step 3: Allow for Simultaneous Gesture Recognizers

Since we will be using multiple gesture recognizers at the same time, we will need to configure our ViewController to support [Simultaneous Gesture Recognizers](https://guides.codepath.com/ios/Using-Gesture-Recognizers#using-simultaneous-gesture-recognizers).

### Step 4: Make the Image View Scalable 

![Make the Image View Scalable gif|200](https://i.imgur.com/AkacSCN.gif)  
  
Within the `didPinch` method... 
- Access the **scale** parameter of the Pinch Gesture Recognizer and store it in a constant.
- Access the view that was pinched and store it in a constant.
   - `sender.view` returns a generic UIView so we need to specify that we are working with a UIImageView using `as! UIImageView`.
- Store the previous transform state of the imageView in a constant.
- Modify the scale component of the imageView's transform property.
   - NOTE: We use `CGAffineTransformScale` instead of `CGAffineTransformMakeScale`. This is because `CGAffineTransformScale` allows us to add to the previous transform state as an argument, `previousTransfrom` whereas `CGAffineTransformMakeScale` will overwrite it completely. [Combining Transforms](https://guides.codepath.com/ios/Using-View-Transforms#combining-transforms)
   - NOTE: CGAffineTransformScale' has been replaced by instance method 'CGAffineTransform.scaledBy(x:y:) (updated: 10/31/17)
- Set the scale of the UIPinchGestureRecognizer back to 1.
   - Resetting the scale is necessary because we are adding the scale to the `previousTransform` each time the method is called. If we didn't reset the scale, each time around the scale that was added to the `previousTransform` would be doubled and our Image View would scale out of control! But don't take my word for it, run the app without resetting the scale back to 1 and see what happens! 

```swift
@IBAction func didPinch(sender: UIPinchGestureRecognizer) {
let scale = sender.scale
let imageView = sender.view as! UIImageView
imageView.transform = CGAffineTransformScale(imageView.transform, scale, scale)
sender.scale = 1
}
```

### Step 5: Make the Image View Rotatable 
  
![Make the Image View Rotatable gif|200](https://i.imgur.com/aqzTp2A.gif)  
  
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
   imageView.transform = CGAffineTransformRotate(imageView.transform, rotation)
   sender.rotation = 0
}
```
