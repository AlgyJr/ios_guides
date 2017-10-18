Xcode allows us to choose from 3 different modal transitions, Cover Vertical, Flip Horizontal and Cross Dissolve. Unfortunately, there is no provided way to adjust these stock transitions; they are what they are. Fortunately, we can create all types of cool custom transitions to fit all your app building needs using a few custom transition classes that we will provide. 

![custom transition tumblr example](https://i.imgur.com/1Sx2rfi.gif)  

### Step 1: Add Custom Transition Files to your Project.  

[Download the Custom Transition Swift Files](https://www.dropbox.com/s/9shewnjl09kzett/Transition%20Files.zip?dl=0) and drag them into your project. The **Base Transition** file contains the "behind the scenes" code to execute our transition. The **Fade transition** describes what happens during the transition, a fading transition between ViewControllers. 

![add transition files to project](https://i.imgur.com/vWq788n.gif)  

### Step 2: Create a Modal Segue.  

[[Create the Segue|Using-Modal-Transitions#step-1-create-the-segue]]

### Step 3: Define the Transition Variable.  

Define a "global" variable for the custom transition *above* the viewDidLoad method. It will be of type, ``FadeTransition``.  

```swift
var fadeTransition: FadeTransition!
```

### Step 4: Setup the prepare method 

The ``prepare()`` method runs any code that we want right before the segue happens. It's kind of like the ``viewWillAppear`` of the segue world.

- Remove the comment ``/* */`` to activate the ``prepare`` method and add the following code within the ``prepare`` method.  

```swift
override func prepare(for segue: UIStoryboardSegue, sender: AnyObject?) {

// Access the ViewController that you will be transitioning too, a.k.a, the destinationViewController.
var destinationViewController = segue.destinationViewController

// Set the modal presentation style of your destinationViewController to be custom.
destinationViewController.modalPresentationStyle = UIModalPresentationStyle.Custom

// Create a new instance of your fadeTransition.
fadeTransition = FadeTransition()

// Tell the destinationViewController's  transitioning delegate to look in fadeTransition for transition instructions.
destinationViewController.transitioningDelegate = fadeTransition

// Adjust the transition duration. (seconds)
fadeTransition.duration = 1.0
}
```

### Step 5: Run Your App!
