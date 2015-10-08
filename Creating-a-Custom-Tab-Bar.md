Sometimes the standard iOS tab bar just doesn't have the look or functionality you need for your app. In this case, you need to create a custom tab bar from scratch.


### Step 1: Setup your StoryBoard

- Add a UIViewController to storyboard to function as your main tab bar ViewController.
- Add UIView to serve as the *bar* part of the Tab Bar.
- Add UIButtons for each tab. Configure their default and selected images.
- Add a UIView that fills the screen above the tabBar, ``contentView``
- Add UIViewControllers that correspond to each tab. (5 tabs = 5 ViewControllers)
- Give each UIViewController a unique StoryBoard ID.
- Add what ever content you want in each ViewController. (We will be pulling in the content from particular ViewController when it's respective tab is tapped.)  

### Step 2: Hook up IBOutlets

- Create an outlet for the *contentView* by ``ctrl + drag``
- Create an IBOutlet array, ``buttons`` to hold your tab bar buttons.
   - ``@IBOutlet var buttons: [UIButton]!``
   - ``ctrl + drag`` outlets from all your buttons to the array.

### Step 3: Create Your Instance Variables (global variables)

- Define variables to hold each ViewController associated with a tab.

```Swift
var homeViewController: UIViewController!
var searchViewController: UIViewController!
var accountViewController: UIViewController!
var trendingViewController: UIViewController!
```

We will use an array to hold all are ViewControllers. That way, we can simply pluck out any particular ViewController we want based on the button that was pushed.
- Define a variable for an array to hold the ViewControllers, ``viewControllers``.

```Swift
var viewControllers: [UIViewController]!
```  

- Define a variable to keep track of the tab button that is selected. Set it to an initial value of ``0``.

```Swift
var selectedIndex: Int = 0
```

### Step 3: Set up communication to View Controllers 

Finally, you have to connect the view controller in Storyboard with the Swift view controller you just created. Do that by selecting the view controller and setting the Identity Inspector, as shown below.

![Identity|700](http://i.imgur.com/FjGouJ9.gif)

Make sure that the Module section gets updated from None to your current project name.  In the example, it got updated to "Current - myApp".