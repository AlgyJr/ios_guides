## Basic Use Case
![textField Facebook GIF|200](http://i.imgur.com/HnN3gMR.gif)

Often, you will want to change or update elements in your app as the user types into a textField. Setting button states as well as updating other labels are common use cases for registering textField events.

### Step 1: Create action for textField event

*Right-Click* on the textField to access the **Editing Changed** event under, **Sent Events**. *ctrl + drag* from the little circle to the right of the event the Swift file associated with your ViewController. Name the action something that makes sense, like "editingChanged". 
![Add textField Action GIF|800](http://i.imgur.com/lm48mx1.gif)  
You have now created an action that will call your method, "editingChanged", each time the user edits text inside the textField.

### Step 2: Add code to be run when the method is called

In this example, we will check to see if either the username field or password field is empty and set the button state accordingly.

```Swift
// create a conditional statement: if the username OR password fields are empty than...
if usernameField.text!.isEmpty || passwordField.text!.isEmpty {
  // set the button state to disabled
  loginButton.enabled = false
// otherwise
} else {
  // set the button state to enabled
  loginButton.enabled = true
  }
}
``` 

### Step 3: Sharing methods between multiple actions

In this use case example, we want the ``editingChanged()`` method to be called when the user edits the username field as well as the password field. Thankfully, different events can share the same method. We can use the same technique of adding an action triggered by a textField event from step 1 of this guide, but this time, *ctrl + drag* right over your existing method until it is highlighted and release.

![Actions sharing method GIF |800](http://i.imgur.com/NmniiJ7.gif)  
