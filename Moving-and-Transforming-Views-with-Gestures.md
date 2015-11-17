Interactive Gestures can really bring your UI to life! In this guide we will explore some common use-cases for moving and transforming views based on gesture input.

## Use Case: Slide Out Tray
Slide out trays, Hamburger menus, and any other view you want to move by sliding your finger can be configured using a UIPanGesture Recognizer.

### Step 1: Add a UIView to Serve as Your Tray
You can [add a View in Storyboard](https://guides.codepath.com/ios/Creating-Nested-Views#step-1-add-the-parent-view) or [add a view programmatically](https://guides.codepath.com/ios/Programmatically-Creating-Views)
 
### Step 2: Add and Configure your Pan Gesture Recognizer.
- Attach a UIPanGestureRecognizer to your Tray view and create an Action/Method. We will call our method, `didPanTray`. You can [add a Gesture Recognizer in Storyboard](https://guides.codepath.com/ios/Using-Gesture-Recognizers#add-and-configure-a-gesture-recognizer-in-storyboard) or [add a Gesture Recognizer programmatically](https://guides.codepath.com/ios/Using-Gesture-Recognizers#programmatically-add-and-configure-a-gesture-recognizer)

### Step 3: Define a Variable to Hold the Initial Position of the View
Define an **Instance Variable** that can be accessed throughout the View Controller. We define these variables at the top of the ViewController Swift file, right above the `viewDidLoad` method.

```swift
var initialTrayCenter: CGPoint!
```

### Step 4: Make the Tray Draggable
The code to make the Tray draggable will go inside our `didPanTray` method.
NOTE: If you created your Gesture Recognizer and added an Action in Storyboard, the method will be proceeded by `@IBAction`.

```swift
@IBAction func didPanTray(sender: UIPanGestureRecognizer) {
    
}
``` 

- Within the `didPanTray` method, we want to access the **translation** property of the UIPanGestureRecognizer and store it in a variable. This will tell us how far our finger has moved from the original "touch-down" point as we drag. We will also print the translation value to the console to get a feel for what it means. You should see that the translation is a **CGPoint** with values for the x and y components. 

```swift
var translation = sender.translationInView(view)
print(translation)
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

### 3: Animate Tray to Open or Closed Positions
When a user stops panning the Tray, we will want the tray to animate to either an open or closed position. We will infer that if the users last gesture movement was downward, they intend to close the tray. Conversely, if they are NOT panning down, they must be panning up, and intend to open the tray.  
  
We can tell which way a user is panning by looking at the gesture property, **velocity**. Like translation, velocity has a value for both x and y components. If the **y component** of the velocity is a **positive** value, the user is **panning down**. If the **y component** is **negative**, the user is **panning** up.
