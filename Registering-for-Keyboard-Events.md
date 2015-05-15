
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

### Step 3: Getting the keyboard properties

You'll want a variety of properties from the keyboard. You'll want the size, but it's also useful to have the animation properties such as duration and curve so that you can match the animation of the keyboard.

See this [gist](https://gist.github.com/timothy1ee/bce97b123cfd90b83971) for a code snippet.

## Moving text fields with keyboard appearance/disappearance