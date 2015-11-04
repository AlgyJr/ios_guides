UITextFields are views which a user can type into. Anytime you need to enter a username, password, or type a text message, you will utilize a UITextField. There are many properties you can set to customize the look and feel of your TextField. You can also check what a user has typed into the TextField and have your app respond accordingly.

## Use Case 1: Log In Screen  
![Logging In Demo gif](http://i.imgur.com/F9HuvZk.gif)

### Step 1: Add Elements to Storyboard

- Add TextFields 
   - Email/Username TextField
   - Password TextField
- Add the "Log In" Button [Add a Button from Object Library](https://github.com/codepath/ios_guides/wiki/Configure-a-Button#step-1-add-button-from-object-library)
- Add the UIActivityView. [Add an Activity Indicator](https://github.com/codepath/ios_guides/wiki/Using-UIActivityIndicatorView#step-1-add-activity-indicator-to-storyboard)
 
![Add Elements to Storyboard gif](http://i.imgur.com/muYoQQ8.gif)  

### Step 2: Configure Text Field Properties

There are many properties you can set to customize your text field. We will want "suggestions" to tell the users what to input in each text field. In this Use Case, we already have a white background image behind the text fields, so we will set the **Border Style** to **Transparent**. Finally, we want the password text field to show dots instead of the actual text so nobody steals our password!

- Set the **Placeholder** text to "Email" and "Password".
- Select the **Border Style**, transparent.  
- Select **Secure Text Entry** for password text field  
  
![Set TextField Properties](http://i.imgur.com/iOIvlMG.gif)  

### Step 3: Configure Button Properties

- Delete the button text and set the image for each button state. [Configure Various Button States](https://github.com/codepath/ios_guides/wiki/Configure-a-Button#step-3-configure-various-button-states)


### Step 4: Configure Activity Indicator Properties

- Change the style to white. 
- Select, "Hides When Stopped". [Set Default Properties](https://github.com/codepath/ios_guides/wiki/Using-UIActivityIndicatorView#step-2-set-default-properties)  
![Activity Indicator Style](http://i.imgur.com/ib87r65.png)

### Step 5: Create Outlets for Elements

In order to communicate with your Text Fields, Button and Activity Indicator in your Swift ViewController file, you will need to create outlets.

- Create an outlet from each Element in Storyboard to your Swift ViewController file by ctrl + dragging from the TextField in Storyboard to your Swift ViewController file.
   - NOTE: All outlets are created near the top of the Swift ViewController file just above the `viewDidLoad` method and right below the `class ViewController: UIViewController {`.  
 
- Name your outlets something like...
   - `emailField`
   - `passwordField`
   - `loginButton`
   - `loginIndicator`

![Create Outlet for each element](http://i.imgur.com/uPNEzla.gif)

### Step 6: Create an Action for the Log In Button

When the user taps the "Log In" Button, we will want to check what they typed in to the text fields and then respond accordingly by calling a function.

- Create an Action from your "Log In" button to your Swift ViewController file. Name your action, **didPressLogin** [Create an Action to Call a Function](https://github.com/codepath/ios_guides/wiki/Configure-a-Button#step-5-call-a-function-when-the-button-is-tapped)

### Step 7: Use Conditional Statement to Check TextField Contents

Within our `didPressLogin` method, we want to check to see what was entered into the `emailField` and `passwordField`. If the contents of both fields match the email and password we are looking for, we will run some code. If either of the fields do not match, we will run some other code

- We can access the text property of a TextField using `textField.text`
- To check if the contents of just the email TextField matches what we are looking for, we can create a conditional if statement,  `if emailField.text == "Text we are looking for" {  }`.
   - NOTE: `==` is used when comparing values to be equivalent. 
- Since we want **both** TextFields to match we can say, within our `didPressLogin` method...

```swift
@IBAction func didPressLogin(sender: AnyObject) {
        
if emailField == "Text we are looking for" && passwordField == "Other text we are looking for" {
   // Code that runs if both email and password match the text we are looking for in each case       
} else {
   // Code that runs if either the email or password do NOT match the text we are looking for in each case        
}
```
   - NOTE: `&&` is used to when `this` **and** `that` have to be **true** to meet the condition.

### Step 8: Do Stuff Within the Method and Conditional Statement
Now, let's look at some common things you might want to do in the case that the email and password match or else do NOT match.

- Start animating the Activity Indicator and set the button state immediately when the button is pressed, i.e. before the conditional statement. 
   - `loginIndicator.stopAnimating()`
   - `loginButton.selected = true`
- Within the conditional statement, if the Email and Password have been entered correctly, you can tell your UIActivityIndicatorView to stop animating `loginIndicator.stopAnimating()` and take the user to the next screen using `performSegueWithIdentifier("yourSegue", sender: nil)` 
   - **NOTE:** You will need to create a modal Segue and give it an ID. [Create the Segue](https://github.com/codepath/ios_guides/wiki/Using-Modal-Transitions#step-1-create-the-segue), [Triggering the Transition Manually](https://github.com/codepath/ios_guides/wiki/Using-Modal-Transitions#triggering-the-transition-manually)
- Within the conditional statement, if the Email or Password are incorrect, stop the UIActivityIndicatorView from animating, `loginIndicator.stopAnimating()` and create/show an alert telling the user that "email or password is incorrect". [Using UIAlertController](https://guides.codepath.com/ios/Using-UIAlertController)
- You will need to put the above code within a delay method to give the UIActivityIndicatorView time to animate. Add this [Common.swift](https://www.dropbox.com/s/mzfmjlvv863x95e/Common.swift?dl=0) file to your project. [Use the Delay Method](https://github.com/codepath/ios_guides/wiki/Calling-a-Method-After-Delay#step-2-use-the-delay-method)   

Example `didPressLogin` method might look like this...

```swift
@IBAction func didPressLogin(sender: AnyObject) {
        // Start animating the activity indicator
        loginIndicator.startAnimating()
        // Set the Button state to "Selected"
        loginButton.selected = true
        // If both the email and password fields match what we are looking for...
        if emailField.text == "Text we are looking for" && passwordField.text == "Other text we are looking for" {
            // Delay for 2 second.
            delay(2, closure: { () -> () in
                // Stop animating the activity indicator.
                self.loginIndicator.stopAnimating()
                // Set the button state back to default, "Not Selected".
                self.loginButton.selected = false
                // perform the Segue to the next screen.
                self.performSegueWithIdentifier("yourSegue", sender: nil)
            })
        // Otherwise, email or password are incorrect so...
        } else {
            // Delay for 2 second
            delay(2, closure: { () -> () in
                // Stop animating the activity indicator.
                self.loginIndicator.stopAnimating()
                // Set the button state back to default, "Not Selected".
                self.loginButton.selected = false
                // Create and Show UIAlertController...see guide, Using UIAlertController
            })
        }
    }
```
   