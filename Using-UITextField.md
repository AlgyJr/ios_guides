UITextFields are views which a user can type into. Anytime you need to enter a username, password, or type a text message, you will utilize a UITextField. There are many properties you can set to customize the look and feel of your TextField. You can also check what a user has typed into the TextField and have your app respond accordingly.

### Add a Text Field in Storyboard

- Drag and drop a UITextField from the Object Library onto your ViewController in Storyboard.  
  
![Add UITextField gif](http://i.imgur.com/VJwVsWu.gif)  

## Use Case 1: Log In Screen

### Configure Text Field Properties

There are many properties you can set to customize your text field. We will want "suggestions" to tell the users what to input in each text field. In this Use Case, we already have a white background image behind the text fields, so we will set the **Border Style** to **Transparent**. Finally, we want the password text field to show dots instead of the actual text so nobody steals our password!

- Set the **Placeholder** text to "Email" and "Password".
- Select the **Border Style**, transparent.  
- Select **Secure Text Entry** for password text field  
  
![Set TextField Properties](http://i.imgur.com/iOIvlMG.gif)  

