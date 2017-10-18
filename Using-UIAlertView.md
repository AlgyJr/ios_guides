**NOTE** UIAlertView has been deprecated since iOS 8.  Use [[Using UIAlertController]] instead.

![Alert|250](https://i.imgur.com/cMAZh9H.gif)

### Step 1: Create the UIAlertView

Create and show the alertView as below. Title, message, delegate, cancelButtonTitle, otherButtonTitles are all optional.

```
var alertView = UIAlertView(title: "Title", message: "Message", delegate: self, cancelButtonTitle: "OK", otherButtonTitles: "Option 1", "Option 2")
alertView.show()

```

If you don't have any otherButtonTitles, you should omit the parameter, as below.

```
var alertView = UIAlertView(title: "Title", message: "Message", delegate: self, cancelButtonTitle: "OK")
alertView.show()

```

### Step 2: Determining which button was tapped

Set your view controller to be a UIAlertViewDelegate

```
class MyViewController: UIViewController, UIAlertViewDelegate {
	...
}

```

Implement the UIAlertView event method, as shown below.

```
func alertView(alertView: UIAlertView!, clickedButtonAtIndex buttonIndex: Int) {
	// buttonIndex is 0 for Cancel
	// buttonIndex ranges from 1-n for the other buttons.	
}

```

## Other Tasks

### Dismissing the UIAlertView

Sometimes, you may want to dismiss an alert view programatically before a user dismisses it by tapping a button.

```
alertView.dismissWithClickedButtonIndex(0, animated: true)

```
