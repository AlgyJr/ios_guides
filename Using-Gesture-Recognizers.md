Gesture recognizers are a powerful and easy to use tool for handling user gestures like tap or pinch and performing actions like triggering animations or view changes. This is a quick guide for the steps in setting up gesture recognizers.

## Add and Configure a Gesture Recognizer in Storyboard

### Step 1: Choose a Gesture Recognizer
Choose a Gesture Recognizer from the Object Library based on the functionality your app needs.  
![Choose a Gesture Recognizer from Object Library gif|200](http://i.imgur.com/evQqzAL.gif)

### Step 2: Drag and Drop a Gesture Recognizer into your View
Drag a Gesture recognizer from the Object Library and drop it onto the View you want the Gesture to be attached to. 
- NOTE: You can see which view the Gesture Recognizer is attached to by ctrl + Clicking on the Gesture Recognizer in the Document Outline.

![Drag and Drop a Gesture Recognizer into your View](http://i.imgur.com/Yv8WOcP.gif)

### Step 3: Create an Action for your Gesture Recognizer
Ctrl-drag from your Gesture Recognizer to the ViewController swift file to create an action. It's often easier to drag from the Gesture Recognizer in the Document Outline. 
- NOTE: Be sure to change the type from **AnyObject** to the specific Gesture Recognizer you are using. i.e. UIPanGestureRecognizer, etc..  

![Create an Action for your Gesture Recognizer](http://i.imgur.com/ddu28U5.gif)

## Programmatically Add and Configure a Gesture Recognizer

### Step 1: Choose a gesture recognizer

There are many builtin gesture recognizers in iOS, and you can also create your own. Choose one from the following list.

- UITapGestureRecognizer. Can be configured with the number of required taps to handle single, double, or more taps.
- UILongPressGestureRecognizer. Can be configured with the required delay to be considered a long press. Defaults to 0.5 s.
- UIPanGestureRecognizer
- UIPinchGestureRecognizer. Used for pinch in and pinch out, this gesture recognizer continuously outputs to a `scale` property as the user moves their finger.
- UIRotationGestureRecognizer. Continuously outputs to `rotation` in radians.
- UIScreenEdgePanGestureRecognizer. Can be configured with which edge to detect pans from. Only detects pan movements that begin from the specified edge.

### Step 2: Instantiate the gesture recognizer

You can create the gesture recognizer in Storyboard or Interface Builder by dragging the gesture recognizer onto the view you want to detect gestures for.

Alternatively, if you want to create the gesture programmatically, it is common to create the gesture recognizers in the `viewDidLoad` method, as shown below. After you create the gesture recognizer, attach it to the view you want to detect gestures on.

#### Example 1: Tap gesture recognizer

```swift
override func viewDidLoad() {
   // The onCustomTap: method will be defined in Step 3 below.
   var tapGestureRecognizer = UITapGestureRecognizer(target: self, action: "onCustomTap:")

   // Optionally set the number of required taps, e.g., 2 for a double click
   tapGestureRecognizer.numberOfTapsRequired = 2;

   // Attach it to a view of your choice. If it's a UIImageView, remember to enable user interaction
   yourView.userInteractionEnabled = true
   yourView.addGestureRecognizer(tapGestureRecognizer)
}

```

#### Example 2: Pan gesture recognizer

```swift
override func viewDidLoad() {
   // The onCustomPan: method will be defined in Step 3 below.
   var panGestureRecognizer = UIPanGestureRecognizer(target: self, action: "onCustomPan:")

   // Attach it to a view of your choice. If it's a UIImageView, remember to enable user interaction
   yourView.userInteractionEnabled = true
   yourView.addGestureRecognizer(panGestureRecognizer)
}

```

### Step 3: Implement the event handler method

When the gesture recognizer detects the gesture, it will call the event handler method on the target. The event handler method is specified when you created the gesture recognizer in Step 2. Declare the method in the interface and implement it.

Gesture recognizers call the same selector as it transitions through various states. For example, a pan gesture recognizer calls the selector when the user first touches down on the view, and then it calls the selector repeatedly as the user drags their finger across the screen, and finally it calls the selector one last time when the user lifts their finger off the screen.

#### Example 1: Tap gesture recognizer


```swift
func onCustomTap(sender: UITapGestureRecognizer) {
   var point = sender.locationInView(view)

   // User tapped at the point above. Do something with that if you want.
}

```

#### Example 2: Pan gesture recognizer

```swift
func onCustomPan(sender: UIPanGestureRecognizer) {
   var point = sender.locationInView(view)
   var velocity = sender.velocityInView(view)
   var translation = sender.translationInView(view)
    
   if sender.state == UIGestureRecognizerState.Began {
      println("Gesture began at: \(point)")
   } else if sender.state == UIGestureRecognizerState.Changed {
      println("Gesture changed at: \(point)")
   } else if sender.state == UIGestureRecognizerState.Ended {
      println("Gesture ended at: \(point)")
   }
}

```

#### Example 3: Pinch Gesture Recognizer
```swift
func didPinchNewFace(sender: UIPinchGestureRecognizer) {

   // get the scale value from the pinch gesture recognizer
   let scale = sender.scale
        
 
}
```

## Other Gesture Recognizer Tasks

Below are some common tasks that you might have with gesture recognizers.

### Using Simultaneous Gesture Recognizers

By default, it won't work if you add multiple gesture recognizers to the same view. In order to do that, you have to specifically enable it.

#### Step 1: Set the Gesture Recognizer delegate

For example, if you want to use a pinch and rotate gesture recognizer simultaneously, choose either the pinch or the rotate gesture recognizer (it doesn't matter which one). If you created the gesture recognizer in Interface Builder, create an IBOutlet for it by Ctrl-dragging from the nib.

```swift
pinchGestureRecognizer.delegate = self;

```

#### Step 2: Implement the Gesture Recognizer protocol

Go to the header file of the view controller and add UIGestureRecognizerDelegate to the list of protocols, as shown below.

```swift
class MainViewController : UIViewController, UIGestureRecognizerDelegate
...
@end

```

Copy the following UIGestureRecognizerDelegate method into the Swift file.

```swift
func gestureRecognizer(gestureRecognizer: UIGestureRecognizer, shouldRecognizeSimultaneouslyWithGestureRecognizer otherGestureRecognizer: UIGestureRecognizer) -> Bool {
return true
}
```

Now the pinch and scale gesture recognizers should work simultaneously.


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