By default, modal transitions can only be one of a few stock transitions. As of iOS 7, its easy to create custom, interactive transitions. These transitions can also be used to customize navigation and tab bar controller transitions.

### Step 1: Setting up the Transition

Create a modal transition in the Storyboard, as usual. Before a segue occurs, the framework will call a method called `prepareForSegue` in the source view controller. You'll have to override that method and specify that you want a custom transition.

```
override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject!) {
    var destinationVC = segue.destinationViewController as UIViewController
    destinationVC.modalPresentationStyle = UIModalPresentationStyle.Custom
    destinationVC.transitioningDelegate = self

}

```

The example above assumes that you only have one segue originating from the view controller. If you have multiple segues, then you'll have to check the segue identifier so you can configure the correct transition.

### Step 2: Implementing the Transitioning Delegate methods

Declare the `UIViewControllerTransitioningDelegate` and `UIViewControllerAnimatedTransitioning` protocols in the class definition. Its also convenient to add a property to track whether the view controller is being presented or dismissed.

```
class MyViewController: UIViewController, UIViewControllerTransitioningDelegate, UIViewControllerAnimatedTransitioning {

var isPresenting: Bool = true

...
}

```

Implement the transition delegate methods.

```
func animationControllerForPresentedController(presented: UIViewController!, presentingController presenting: UIViewController!, sourceController source: UIViewController!) -> UIViewControllerAnimatedTransitioning! {
    isPresenting = true
    return self
}
    
func animationControllerForDismissedController(dismissed: UIViewController!) -> UIViewControllerAnimatedTransitioning! {
    isPresenting = false
    return self
}       

```

Finally, implement the method that actually controls the transition.

```
func transitionDuration(transitionContext: UIViewControllerContextTransitioning) -> NSTimeInterval {
    // The value here should be the duration of the animations scheduled in the animationTransition method
    return 0.4
}
    
func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
	// TODO: animate the transition in Step 3 below
} 

```

### Step 3: Animate the transition

The final step is to implement the animateTransition method above which controls the custom transition. In animateTransition, you have access to a containerView which will contain the views of both the source and destination view controller. You also have access to the source and destination view controller.

In order to animate the transition, you generally have to add the view of the destination view controller to the container view and schedule the desired animations. In the completion block of the animation, it's important to call `completeTransition` on the transitionContext to return it to a consistent state.

For example, to fade the view controller, do the following:

```
func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
    println("animating transition")
    var containerView = transitionContext.containerView()
    var toViewController = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)!
    var fromViewController = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey)!
    
    if (isPresenting) {
        containerView.addSubview(toViewController.view)
        toViewController.view.alpha = 0
        UIView.animateWithDuration(0.4, animations: { () -> Void in
            toViewController.view.alpha = 1
            }) { (finished: Bool) -> Void in
                transitionContext.completeTransition(true)
        }
    } else {
        UIView.animateWithDuration(0.4, animations: { () -> Void in
            fromViewController.view.alpha = 0
            }) { (finished: Bool) -> Void in
                transitionContext.completeTransition(true)
                fromViewController.view.removeFromSuperview()
        }
    }
}


```

## Interactive Transitions

Interactive transitions are a great way to add control to the transition. For example a person could pinch in and out revealing and hiding the next screen as they pinch.

### Step 1: Setup

You should have followed the steps above, now you just need to a variable `interactiveTransition` as a class variable. You can see it below:

```
class MyViewController: UIViewController, UIViewControllerTransitioningDelegate, UIViewControllerAnimatedTransitioning {

var isPresenting: Bool = true
var interactiveTransition: UIPercentDrivenInteractiveTransition!
...
}

```

### Step 2: Adding your interaction controller delegate method

When you cmd + click `UIViewControllerTransitioningDelegate` you should see this function `interactionControllerForPresentation`. Add this to your ViewController file and initialize the interactive transition.

```
func interactionControllerForPresentation(animator: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
        interactiveTransition = UIPercentDrivenInteractiveTransition()
        //Setting the completion speed gets rid of a weird bounce effect bug when transitions complete
        interactiveTransition.completionSpeed = 0.99
        return interactiveTransition
    }

```

### Step 3: Add your gesture code

This will make your interaction transition interactive based on the user's actions. Here we are using a pinchGestureRecognizer. You can use other gestures too.

````
func onPinch(sender: UIPinchGestureRecognizer) {
    var scale = sender.scale
    var velocity = sender.velocity
    if (sender.state == UIGestureRecognizerState.Began){
        //blueSegue is the name we gave our modal segue, this also starts our interactive transition
        performSegueWithIdentifier("blueSegue", sender: self)
    } else if (sender.state == UIGestureRecognizerState.Changed){
        //We are dividing by 7 here since updateInteractiveTransition expects a number between 0 and 1
        interactiveTransition.updateInteractiveTransition(scale / 7)
    } else if sender.state == UIGestureRecognizerState.Ended {
        if velocity > 0 {
            interactiveTransition.finishInteractiveTransition()
        } else {
            interactiveTransition.cancelInteractiveTransition()
        }
    }
}
````
