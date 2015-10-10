Often times, we want to create our own Navigation Bars in order to get the right look for our app.

### Step 1: Initial Setup
 
- Select the Initial ViewController and choose, **Menu Bar --> Editor --> Embed In --> Navigation Controller**.
- With the NavigationController selected, go to the *properties* pane and uncheck, "Show navigation bar".  
![Embed In NavigationController](http://i.imgur.com/TDbx05T.gif)


### Step 2: Create Push Segues to Transition Forward

When using NavigationControllers, we will use **Push** Segues.
- Add a button to your first ViewController (After the NavigationController)
- *ctrl + drag* from the first ViewController to the second and select **Push** segue.  
![Add a Push Segue](http://i.imgur.com/RfNIR62.gif)
### Step 3: Add Nav Bar Image and Button in Storyboard

- Drag an ImageView containing your custom navigation bar image to the top of each ViewController.
- Add a button on top of the "Back" part of the navigation bar image and delete the button text.
![Add Nav Bar](http://i.imgur.com/dUYB6yZ.gif)


### Step 4: Configure Back Button  

In order to hook up your button to some code, you will need a ViewController file. New projects come with one already created, but is is good practice to create a new custom ViewController file and name it something relevant to the function of the ViewController it will be associated with in the Storyboard.
- Create a new ViewController file. 
![Create a new ViewController file](http://i.imgur.com/LBAIYZT.png)
- *ctrl + drag* from the button to your ViewController file to create an action. [Configure a Button](https://github.com/codepath/ios_guides/wiki/Configure-a-Button)
- Within your button action, add code to go back to the previous ViewController. [Navigation Controllers]()

```Swift
navigationController!.popViewControllerAnimated(true)
```

### Step 5: Run your app!  
![Nav Bar Sim example](http://i.imgur.com/XQ7duiZ.gif)  

