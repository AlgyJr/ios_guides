You can choose whether to display as an alert or action sheet style. 

## Using Alert style

The alert style presents modally in the center of their presenting view controllers. 

![Alert|250](http://i.imgur.com/cMAZh9H.gif)

### Step 1: Create the UIAlertController

Create the alert controller as below, setting preferredStyle to Alert

```
let alertController = UIAlertController(title: "Title", message: "Message", preferredStyle: .Alert)
```

### Step 2: Add buttons

Create a UIAlertAction for each button to display and respond to.

```
// create a cancel action
let cancelAction = UIAlertAction(title: "OK", style: .Cancel) { (action) in
    // handle cancel response here. Doing nothing will dismiss the view.
}
// add the cancel action to the alertController
alertController.addAction(cancelAction)

// create an OK action
let OKAction = UIAlertAction(title: "OK", style: .Default) { (action) in
    // handle response here.
}
// add the OK action to the alert controller
alertController.addAction(OKAction)


```

Adding two buttons will place them side by side in the alert. Adding more than two buttons stacks them in the view similar to the ActionSheet style.


### Step 3: Show the UIAlertController

```
presentViewController(alertController, animated: true) {
    // optional code for what happens after the alert controller has finished presenting
}
```

## Using Action Sheets style

The action sheet style anchors the view to the bottom of the presenting view controller.

![Action|250](http://i.imgur.com/8JfoPh3.gif)

### Step 1: Create the UIAlertController

Create the alert controller as below, setting preferredStyle to ActionSheet

```
let alertController = UIAlertController(title: "Title", message: "Message", preferredStyle: .ActionSheet)
```

### Step 2: Add buttons

Create a UIAlertAction for each button to display and respond to. The destructive style options shows the button text in red.

```
let logoutAction = UIAlertAction(title: "Log Out", style: .Destructive) { (action) in
    // handle case of user logging out
}
// add the logout action to the alert controller
alertController.addAction(logoutAction)     

let cancelAction = UIAlertAction(title: "Cancel", style: .Cancel) { (action) in
    // handle case of user canceling. Doing nothing will dismiss the view.
}
// add the cancel action to the alert controller
alertController.addAction(cancelAction)
```

### Step 3: Show the UIAlertController

```
presentViewController(alertController, animated: true) {
    // optional code for what happens after the alert controller has finished presenting
}
```
