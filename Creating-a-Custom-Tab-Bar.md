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

### Step 5: Create a Shared Action for the Buttons.

Since we will be keeping track of which button was tapped, all the buttons can share the same action method.

- ``ctrl + drag`` from your first button to the TabBarViewController to create an action.
   - Name it something like ``didPressTab``.
   - Change the **Type** from **AnyObject** to **UIButton** (that way we can access special properties only buttons have)
   - ``ctrl + drag`` from every other button to the same ``didPressTab`` so they are all sharing.

### Step 6: Code the ``didPressTab`` method.

When a new tab button is tapped, the goal of this method is to get rid of the ViewController contents that was previously being displayed in the tabBarViewController, and replace it with the new ViewController content that corresponds to the new tab button that was pushed. In order o do this, we need to know two things: the **previous** Button and ViewController that were selected and the **current** Button and ViewController that are now selected.   
  
The ``selectedIndex`` will store the **tag** value of whatever button is selected. We set the initial value of ``selectedIndex`` to **0**, or button 1. When we tap a different button, until we assign ``selectedIndex`` the **tag** value of the **new** button that was pushed, it actually represents the **tag** value of the previous button.  
  
- So, within the ``didPressTab`` method, we can keep track of the **previous** button like this...

```Swift
previousIndex = selectedIndex
```

Remember when we changed **AnyObject** to ``UIButton`` when we hooked up our action? That is so we can access special properties of buttons, like accessing their **tag** value.   
  
- Set the ``selectedIndex`` to the **tag** value of which ever button was tapped.

```Swift
selectedIndex = sender.tag
```

You might be wondering why we don't just call ``selectedIndex``, ``selectedTag`` instead. The reason is because we are going to plug that number into our ``buttons`` and ``ViewControllers`` arrays in order to pluck out the correct item. When you access an item from an array at a certain point, ``buttons[0]`` or ``viewControllers[3]``, that point is called the **index**. The items in an array start at index **0** and go up by a value of **1** each item so that the **2nd** item is at **index 1**, the **3rd** is **index 2** and so on.

So as you can see, it is no coincidence that we set our first button **tag** value to be ``0``, and then stored it at ``index 0`` in our ``buttons array``; just as we did for the corresponding index locations of the ViewControllers stored in our ``viewControllers`` array.  
