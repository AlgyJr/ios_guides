Xcode allows us to choose from 3 different modal transitions, Cover Vertical, Flip Horizontal and Cross Dissolve. Unfortunately, there is no provided way to adjust these stock transitions; they are what they are. Fortunately, we can create all types of cool custom transitions to fit all your app building needs using a few custom transition classes that we will provide. 

![custom transition tumblr example](http://i.imgur.com/1Sx2rfi.gif)  

### Step 1: Add Custom Transition Files to your Project.  

[Download the Custom Transition Swift Files](https://www.dropbox.com/s/x1kz6kymfpzsl0h/Transition%20Files.zip?dl=0) and drag them into your project. The **Base Transition** file contains the "behind the scenes" code to execute our transition. The **Fade transition** describes what happens during the transition. 

![add transition files to project](http://i.imgur.com/vWq788n.gif)  

### Step 2: Create a Modal Segue.  

[[Create the Segue|Using Modal Transitions#heading-Step-1:-Create-The-Segue]]

### Step 3: Define the Transition Variable.  

Define a "global" variable for the custom transition *above* the viewDidLoad method. It will be of type, ``FadeTransition``.  

```Swift
var fadeTransition: FadeTransition!
```

### Step 4: Setup the prepareForSegue Method.  

The ``prepareForSegue`` method runs any code that we want right before the segue happens. It's kind of like the ``viewWillAppear`` of the segue world.

- Remove the 
