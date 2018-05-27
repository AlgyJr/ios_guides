### Add activity indicator to storyboard
- Add the Activity Indicator View to your storyboard from the Object Library
- Choose a style and color
- Select "Hides When Stopped" in order to automatically hide the activity indicator any time it's not animating.</br>
   
   <img src="https://i.imgur.com/EBJYKdf.gif" width="600" />
 

### Create an Outlet
- <kbd>control</kbd> + <kbd>drag</kbd> from activity in storyboard canvas (or document outline) to swift file.</br>
   
   <img src="https://i.imgur.com/Oj0RZyM.gif" width="600" />

### Start / Stop animating
Tell the Activity Indicator View to start animating.

```swift
// Start the activity indicator
activityIndicator.startAnimating()

// Stop the activity indicator
// Hides automatically if "Hides When Stopped" is enabled
activityIndicator.stopAnimating()
```
```objc
// Start the activity indicator
[self.activityIndicator startAnimating];

// Stop the activity indicator
// Hides automatically if "Hides When Stopped" is enabled
[self.activityIndicator stopAnimating];
```
