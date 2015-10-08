Sometimes the standard iOS tab bar just doesn't have the look or functionality you need for your app. In this case, you need to create a custom tab bar from scratch.


### Step 1: Setup your Storyboard

- Add a UIViewController to storyboard to function as your main tab bar ViewController.
- Add UIView to serve as the *bar* part of the Tab Bar.
- Add UIButtons for each tab. 
   - Configure their default and selected images.
   - Set the **tag** value of each button starting with **0** for the first button, **1** for the second and so on.
- Add a UIView that fills the screen above the tabBar. This will serve as our content view.
- Add UIViewControllers that correspond to each tab. (5 tabs = 5 ViewControllers)
- Give each UIViewController a unique Storyboard ID.
- Add what ever content you want in each ViewController. (We will be pulling in the content from particular ViewController when it's respective tab is tapped.)  

### Step 2: Hook up IBOutlets


- Create an outlet for the *contentView* by ``ctrl + drag``

Creating an array of buttons will help us easily keep track of what button is getting pushed based on the **tag** or **index** of the button.
- Create an IBOutlet array named, ``buttons`` to hold your tab bar buttons.
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
- Define a variable for an array to hold the ViewControllers named, ``viewControllers``.

```Swift
var viewControllers: [UIViewController]!
```  

- Define a variable to keep track of the tab button that is selected. Set it to an initial value of ``0``. We will link the button's **tag** value to this variable. So an initial value of ``0`` will reference our first button.

```Swift
var selectedIndex: Int = 0
```

### Step 4: Link your ViewController Variables to the ViewControllers in the Storyboard.

Remember, all the ViewControllers we create in the Storyboard don't actually exist, until we set up a link to them somehow, like a segue. For our custom tab bar, we won't be using segues, so we will have to instantiate them when our main tab bar ViewController first loads.

- Within the ``ViewDidLoad()`` method, access the main Storyboard through code.

```Swift
let storyboard = UIStoryboard(name: "Main", bundle: nil)
```

- Next, instantiate **each** ViewController by referencing ``storyboard`` and the particular ViewController's **Storyboard ID**

```Swift
homeViewController = storyboard.instantiateViewControllerWithIdentifier("HomeViewController")
```

### Step 5 Create a Shared Action for the Buttons.
Since we will be keeping track of which button was tapped, all the buttons can share the same action method.

- ``ctrl + drag`` from your first button to the TabBarViewController to create an action.
   - Name it something like ``didPressTab``.
   - Change the **Type** from **AnyObject** to **UIButton** (that way we can access special properties only buttons have)
   - ``ctrl + drag`` from every other button to the same ``didPressTab`` so they are all sharing.