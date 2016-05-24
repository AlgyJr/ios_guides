Sometimes you have to instantiate view controllers that have been designed in Storyboard.

### Step 1: Set a Storyboard ID

In the Storyboard, select the view controller that you want to instantiate in code. Make sure the yellow circle is highlighted, and click on the Identity Inspector. Set the custom class as well as the field called "Storyboard ID". You can use the class name as the Storyboard ID.

### Step 2: Instantiate the view controller

In order to instantiate the view controller, you need a variable for the storyboard. Most storyboards are named "Main" (this is case sensitive!), but if your storyboard has a different filename, make sure you use that name.

```swift
var storyboard = UIStoryboard(name: "Main", bundle: nil)

var vc = storyboard.instantiateViewControllerWithIdentifier("YourViewController") as YourViewController

```
