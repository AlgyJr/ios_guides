Sometimes, it's convenient to have a long or wide view controller in Storyboard when you're designing long or wide views. If that's the case, configure the view controller to be freeform.
  
![free form](http://i.imgur.com/YmkE1Gh.png) 
  
### Step 1: Set the view controller to be freeform

In the Storyboard, make sure the view controller is selected, then click the Ruler icon, select freeform, and choose your dimensions.

![Free](http://i.imgur.com/EMWaad4.png)

### Step 2: Configure the Autoresizing masks

At runtime, the app will attempt to shrink the view controller back down to a normal phone size. Sometimes, you want the subviews to shrink with it, and sometimes you want it to stay the same size.

For example, if it contains a scrollview, you probably want the scrollview to shrink down. In that case, set the autoresizing mask to have a flexible height and flexible width, as shown below. Tap the arrows in order to highlight them red.

![Flexible height](http://i.imgur.com/eHRC9VN.png)

For a fixed size UIImageView, you probably don't want the view to change dimensions. In that case, unselect the flexible height and width arrows.

![Fixed height](http://i.imgur.com/nLQEfZr.png)