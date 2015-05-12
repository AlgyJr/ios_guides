Push/pop navigation is one of the common types of navigation in iPhone apps, and it is easy to set up. A navigation controller manages a stack of view controllers. It is always initialized with a view controller and view controllers can be pushed onto the stack or removed from the stack.

Follow the steps below to set up a navigation controller using both Storyboard and programatically.

<img src="http://i.imgur.com/JBrxKdh.gif" height="568" width="320" />

## Using Navigation Controllers in Storyboard

### Step 1: Embed a View Controller in a Navigation Controller

![Embed in Navigation|600](http://i.imgur.com/PTRpAFf.gif)

### Step 2: Create push segues to any view controller

![Creating a push segue|600](http://i.imgur.com/tbMhxpi.gif)

### Step 3: Popping a View Controller

By default, navigation controllers provide a navigation bar with a back button. If you want to go back to the previous view controller using code, then you can call the popViewControllerAnimated method, as shown below.

```
navigationController!.popViewControllerAnimated(true)
```
