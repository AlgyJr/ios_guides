### Step 1: Add activity indicator to storyboard
Add the Activity Indicator View to your storyboard from the Object browser and check the animating property.
![Add Activity Indicator|250](http://i.imgur.com/CYS7YwM.gif)

### Step 2: Set default properties
Move the Activity Indicator View inside a button. Set the style of the Activity Indicator to be Large White. Select hidden when stopped and uncheck animating.
![Activity Indicator Large|250](http://i.imgur.com/ydhab4Y.gif)

### Step 3: Start animating on button tap
Add an outlet for the Activity Indicator View in your View Controller. Then, add a tap handler for your button. Inside the tap handler, tell the Activity Indicator View to start animating.
![Activity Indicator Large|250](http://i.imgur.com/ic8fzUT.gif)