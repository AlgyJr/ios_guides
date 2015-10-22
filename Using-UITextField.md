UITextFields are views which a user can type into. Anytime you need to enter a username, password, or type a text message, you will utilize a UITextField. There are many properties you can set to customize the look and feel of your TextField. You can also check what a user has typed into the TextField and have your app respond accordingly.

## Use Case 1: Log In Screen

### Step 1: Add a Text Field in Storyboard

- Drag and drop a UITextField from the Object Library onto your ViewController in Storyboard.  
  
![Add UITextField gif](http://i.imgur.com/VJwVsWu.gif)  

### Step 2: Configure Text Field Properties

There are many properties you can set to customize your text field. We will want "suggestions" to tell the users what to input in each text field. In this Use Case, we already have a white background image behind the text fields, so we will set the **Border Style** to **Transparent**. Finally, we want the password text field to show dots instead of the actual text so nobody steals our password!

- Set the **Placeholder** text to "Email" and "Password".
- Select the **Border Style**, transparent.  
- Select **Secure Text Entry** for password text field  
  
![Set TextField Properties](http://i.imgur.com/iOIvlMG.gif)  

### Step 3: Create Outlets for Text Fields

In order to communicate with your text fields in your code, you will need to create outlets.

- Create an outlet from each TextField to your Swift ViewController file by ctrl + dragging from the TextField in Storyboard to your Swift ViewController file.
   - NOTE: All outlets are created near the top of the Swift ViewController file just above the `viewDidLoad` method and right below the `class ViewController: UIViewController {`.  
  
![Create Outlet from TextField](http://i.imgur.com/Ly4lsSY.gif)


### Step 4: Add a Button for Logging In

- Add a Button for Logging In [Add a Button from Object Library](https://github.com/codepath/ios_guides/wiki/Configure-a-Button#step-1-add-button-from-object-library)

### Step 5: Create an Action for the Log In Button

When the user taps the "Log In" Button, we will want to check what they typed in to the text fields and then respond accordingly by calling a function.

- Create an Action from your "Log In" button to your Swift ViewController file. [Create an Action to Call a Function](https://github.com/codepath/ios_guides/wiki/Configure-a-Button#step-5-call-a-function-when-the-button-is-tapped)