Every screen in your application is implemented by a view controller. A view controller has two parts: the designed part that's in Storyboard and a Swift file that contains the code for that screen.

If a screen in the application doesn't require any code, then it can use the default `UIViewController`. However, as soon as you need to handle events like a user tapping on the screen, then you need to create a custom view controller for that screen.

### Step 1: Adding the View Controller to Storyboard

In Storyboard, drag a new UIViewController onto the canvas, as shown in the figure below.

![New View Controller|700](http://i.imgur.com/oYg1Upk.gif)

### Step 2: Create the Swift View Controller

Each view controller should have a name that describes what kind of information or task it has. It's either displaying information, e.g., HomeFeedViewController, TweetsViewController, or ProfileViewController. Or, it's a form to create data, e.g., CreateUserViewController, CreateCheckInViewController.

To create a new view controller, select File->New->File. View Controllers are often in their own folder or "group" in the project explorer. The figure below shows creating a new view controller and adding it to a new group.

![New View Controller|700](http://i.imgur.com/X2ZjXya.gif)

### Step 3: Associating the View Controller

Finally, you have to connect the view controller in Storyboard with the Swift view controller you just created. Do that by selecting the view controller and setting the Identity Inspector, as shown below.

![Identity|700](http://i.imgur.com/FjGouJ9.gif)

Make sure that the Module section gets updated from None to your current project name.  In the example, it got updated to "Current - myApp".