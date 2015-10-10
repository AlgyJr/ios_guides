Often times, we want to create our own Navigation Bars in order to get the right look for our app.

### Step 1: Initial Setup

- Drag a NavigationController onto the Storyboard. The NavigationController comes hooked up to a UIViewController.
- *Optional Method* If you already created a ViewController select the ViewController and choose, **Menu Bar --> Editor --> Embed In --> Navigation Controller**.
- With the NavigationController selected, go to the *properties* pane and uncheck, "Show navigation bar".

### Step 2: Create Custom Navigation Bar in Storyboard

- Drag an ImageView containing your custom navigation bar image to the top of each ViewController.
- Add a button on top of the "Back" part of the navigation bar image and delete the button text.

### Step 3: Configure Back Button  

In order to hook up your button to some code, you will need a ViewController file. New projects come with one already created, but is is good practice to create a new custom ViewController file and name it something relevant to the function of the ViewController it will be associated with in the Storyboard.
- Create a new ViewController file. 
![Create a new ViewController file](Create a new ViewController file)
- *ctrl + drag* from the button
