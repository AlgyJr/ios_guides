
![Action|250](http://i.imgur.com/8JfoPh3.gif)

### Step 1: Create the UIActionSheet

Create and show the action sheet as below. The destructive title will appear in red.

```
var actionSheet = UIActionSheet(title: "Log Out", delegate: self, cancelButtonTitle: "Cancel", destructiveButtonTitle: "Log Out")
actionSheet.showInView(view)

```

### Step 2: Determining which button was tapped

Set your view controller to be a UIActionSheetDelegate

```
class MyViewController: UIViewController, UIActionSheetDelegate {
	...
}

```

Implement the UIActionSheet event method, as shown below.

```
func actionSheet(actionSheet: UIActionSheet!, clickedButtonAtIndex buttonIndex: Int)
	// buttonIndex is 0 for Cancel
	// buttonIndex ranges from 1-n for the other buttons.	
}

```
