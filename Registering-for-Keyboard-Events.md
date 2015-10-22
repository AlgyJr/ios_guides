
## Basic Use Case
![Keyboard GIF | 250](http://i.imgur.com/bQsqmhT.gif)

Often, you will have to rearrange views when the keyboard is shown or hidden to ensure that the appropriate views and controls are visible while the user is typing. You can register for the begin/end editing events for UITextFields, which works well for a single UITextField. However, when you have many UITextFields, it can get complicated because when one UITextField is ending editing, another might be beginning editing, so it can be hard to tell if the keyboard is actually going away.

The easier way is to register for the keyboard events which are fired when the keyboard is shown or hidden. There is an additional benefit that the keyboard event returns the height of the keyboard which can vary greatly depending on orientation, device, language, and custom keyboards.

This mechanism is called NSNotifications. It is commonly used for system events like keyboard, device rotation, and coming back from or going into standby. For example, when coming back from standby, it's common to refresh the network data of the view controller.

### Step 1: Defining the method to be called

In your view controller, define 2 methods that you want to be called when the keyboard is shown or hidden.

```
func keyboardWillShow(notification: NSNotification!) {
        
}

func keyboardWillHide(notification: NSNotification!) {
        
}

```

You can put the methods above anywhere in the view controller file.

### Step 2: Register for the keyboard events

In the init or viewDidLoad methods, register for keyboard events and tie them to the methods you defined in Step 1.  

```
NSNotificationCenter.defaultCenter().addObserver(self, selector: "keyboardWillShow:", name: UIKeyboardWillShowNotification, object: nil)
NSNotificationCenter.defaultCenter().addObserver(self, selector: "keyboardWillHide:", name: UIKeyboardWillHideNotification, object: nil)

```

Note: You must place a colon in selector names for functions with a parameter, i.e ``"keyboardWillShow:"``

### Step 3: Add a UITextField to the view

![Add UITextField | 250](http://i.imgur.com/HL1Ekq3.gif)

Place a UITextField within the view using Interface Builder. Then, use the Assistant Editor button to reveal the associated ViewController code. Ctrl+click on the text field and drag the blue line to the ViewController in order to create either an Outlet or Action Handler for the text field. We will select Outlet and name the outlet according to the purpose of the UITextField.

### Step 4: Offset the UITextField when the keyboard is shown

![Show Keyboard Offset | 250](http://i.imgur.com/Bjiect2.gif)

Let's use the animation block we created in step 3, within the ```keyboardWillShow``` hook, to animate the text field moving some fixed distance away from the keyboard as the keyboard is shown. Using the animation block will help us match the animation transition between the text field and the keyboard. For simplicity, we will simply move the text field a constant distance towards the top of the screen.

First, create variables for the initial y position of the text field as well as the constant amount we will offset the text field in the y direction.

```
var initialY: CGFloat!
let offset: CGFloat = -50
```

Next, within the ```viewDidLoad``` method record the initial y position of the text field.
```
initialY = username.frame.origin.y
```

 Finally, within the animation block from Step 3, update the y position of the text field to the shown position.
```
self.username.frame.origin = CGPoint(x: self.username.frame.origin.x, y: self.initialY + self.offset)
```

Note: If your keyboard does not show when you click within the text field. Make sure that you have the external keyboard simulation disabled. Go to menu Hardware | Keyboard and make sure "Connect Hardware Keyboard" is unchecked.

### Step 5: Move UITextField back when keyboard is hidden

![Hide Keyboard Offset | 250](http://i.imgur.com/Ag9mO7D.gif)

Now we will simply animate the UITextField back to its original y position when the keyboard is hidden. This will occur within the ```keyboardWillHide``` method. First, copy the ```keyboardWillShow``` method and rename it to ```keyboardWillHide```.  Next, change the line within the animation block to
```
self.username.frame.origin = CGPoint(x: self.username.frame.origin.x, y: self.initialY) 
```

### Step 6: Hide the Keyboard When Tapping Outside the TextField

If you run the simulator now, you notice that when you click on the text field it will animate up as it should, but when you click away you cannot hide the keyboard. So we need to have the keyboard hide event triggered. A common way to do this is to tap away from the text field trigger a keyboard hide. So we find the UITapGesture recognizer in the Object Library at the lower right and drag and drop it within the background view. Next, we Ctrl+click and drag from the Tap Gesture Recognizer to the View Controller shown in the assistant editor. We will select the Action option and name the handler ```onTap```. Within the ```onTap``` method we simply have the line
```
view.endEditing(true)
```
which will trigger the ```keyboardWillHide``` method on our view controller.
