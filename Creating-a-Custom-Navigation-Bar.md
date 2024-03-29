Often times, we want to create our own Navigation Bars in order to get the right look for our app.  
![Nav Bar Sim example](https://i.imgur.com/GlK7TXy.gif)

### Step 1: Add and Setup NavigationController  
[Using Navigation Controllers](http://guides.codepath.com/ios/Using-Navigation-Controllers)

### Step 2: Add Nav Bar Image and Button in Storyboard

- With the NavigationController selected, go to the *properties* pane and uncheck, "Show navigation bar".
- Drag an ImageView containing your custom navigation bar image to the top of each ViewController.
- Add a button on top of the "Back" part of the navigation bar image and delete the button text.
![Add Nav Bar](https://i.imgur.com/dUYB6yZ.gif)

### Step 3: Configure Back Button  

- Create a new ViewController Swift file. [Creating Custom View Controllers](http://guides.codepath.com/ios/Creating-Custom-View-Controllers)
- Add an action from your back button. [Configure a Button](https://github.com/codepath/ios_guides/wiki/Configure-a-Button)
- Add code to go back to the previous ViewController. ``navigationController!.popViewControllerAnimated(true)`` [Using Navigation Controllers](http://guides.codepath.com/ios/Using-Navigation-Controllers)

### Step 4: Run your app! 

