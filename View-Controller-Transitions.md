## Overview

UIKit comes with a set of default transitions for navigation controllers and modal view controllers. For instance, when you push a new view controller onto a navigation controller, it slides in from the right. iOS 7 introduced an API for providing your own custom transitions. In the case of tab bars, you can even add animation where the default implementation provides none.

These transitions do not apply to the container view controllers you write yourself. However, since so many applications are built upon navigation controllers, tab bar controllers, and modals, it's a useful API to understand.

There are two types of transitions. The first is an **animation transition**, which are not interactive. This includes the simple pop animation when you tap "Back" in a navigation controller.

The second type of transition an **interactive transition**. This is seen when you bezel-swipe to go back in a navigation controller. In this transition, the user can begin the transition, scrub back and forth while they make up their mind, and then either complete the transition or cancel it. As an interactive transition has so many possible states, its API has to be more complex than the simple animation transition.

To start, this guide lists the components that work together to make a transition. Next, this guide will cover a simple, non-interactive animation transitions. Finally, it will cover interactive transitions.

## The Components

### The Context Object

The context object, `UIViewControllerContextTransitioning`, is used through transitions to provide details for the code you're going to write. These details include:

* The two view controllers involved
* Their start and end frames
* The container view that contains both view controllers' views.

Finally, it contains a callback you will call when you're done with your transition, to signal that the transition is complete.

### The Transition Delegate

You will fill out a delegate method that returns an "Animation Controller" object, which actually performs the transition animation. The transition delegate method only returns this object.

Different types of view controllers ask for the animation controller using different delegate methods:

* Navigation Controller: UINavigationControllerDelegate's `navigationController(_: ​animationControllerForOperation:fromViewController:​toViewController:)`
* Tab Bar Controller: UITabBarControllerDelegate's `tabBarController(​_:animationControllerForTransitionFromViewController:toViewController:​)`
* Modals: `UIViewControllerTransitioningDelegate`
  * Presentation: `animationControllerForPresentedController(_:presentingController:​sourceController:​)`
  * Dismissal: `animationControllerForDismissedController(_:)`

### The Animation Controller

The animation controller is responsible for simple, non-interactive animations. The name "controller" may be confusing, as it is *not* a subclass of `UIViewcontroller`. It is a protocol with two required methods.

* `transitionDuration(context)`: The duration of your animation
* `animateTransition(context)`: Your animation logic

Your `animateTransition` method must do two things: add the new view controller to the container view, and call `completeTransition(didComplete)` on your transition context object when the transition is complete.

Here is a very simple implementation that just fades between view controller:

```swift
class SimpleAnimationController: NSObject, UIViewControllerAnimatedTransitioning {
    func transitionDuration(transitionContext: UIViewControllerContextTransitioning) -> NSTimeInterval {
        return 0.35
    }

    func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
        let fromViewController = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey)
        let toViewController = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)!
        transitionContext.containerView().addSubview(toViewController.view)
        toViewController.view.alpha = 0.0
        UIView.animateWithDuration(0.35, animations: {
            toViewController.view.alpha = 1.0
            }, completion: { (finished) in
                transitionContext.completeTransition(!transitionContext.transitionWasCancelled())
        })
    }
}
```

To wire this up to your view controller:

```swift
class MyViewController: UIViewController, UINavigationControllerDelegate  {

    override func awakeFromNib() {
        super.awakeFromNib()
        self.navigationController?.delegate = self
    }

    func navigationController(navigationController: UINavigationController, animationControllerForOperation operation: UINavigationControllerOperation, fromViewController fromVC: UIViewController, toViewController toVC: UIViewController) -> UIViewControllerAnimatedTransitioning? {
        return SimpleAnimationController()
    }
}
```

### Interactive Transitions

Before you can write an interactive transition, make sure you have implemented the animation transition, above. UIKit will only check for interactive transition support if it is already using a custom animation controller.

In this example, we'll use a pan gesture to trigger a custom animation for popping off a UINavigationController. To keep everything in one place, we're going to put all the logic in the previous animation controller we've been using. This is a common pattern.

We attach the gesture recognizer, and have it call `didPan` on our transition controller. When we want the interactive animation to kick off, we call `popViewController` as normal.

```swift
class MyFadeTransition: NSObject, UIViewControllerAnimatedTransitioning {
    unowned var transitioningController: UIViewController

    init(transitioningController: UIViewController){
        self.transitioningController = transitioningController
    }

    func didPan(gesture: UIPanGestureRecognizer){
        switch (gesture.state){
        case .Began:
            self.usingGesture = true
            self.transitioningController.navigationController?.popViewControllerAnimated(true)
        // Other gestures states follow...
        }
    }
}
```

Now the view controller will ask the transition delegate for an interactive transition controller. If you return nil, it will just run the previous, non-interactive transition.

```swift
func navigationController(navigationController: UINavigationController, interactionControllerForAnimationController animationController: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
    if myInteractiveTransitionAnimation.usingGesture {
      return myInteractiveTransitionAnimation
    } else {
      return nil
    }
}
```

We used the flag "usingGestures" so the interactive transition only kicks off if the user is performing a gesture.

Next, we would implement the `UIViewControllerInteractiveTransitioning` methods. However, Apple has provided a helpful class you can subclass, which makes things easier, and most people use: `UIPercentDrivenInteractiveTransition`. This object runs our simple animation, records the animation, and lets us scrub between points by calling `updateInteractiveTransition(percent)`.

Here is an abridged version of our code, now subclassing this "Percent Driven" transition.

```swift
class MyFadeTransition: UIPercentDrivenInteractiveTransition, UIViewControllerAnimatedTransitioning {

    // Previous code omitted

    func didPan(gesture: UIPanGestureRecognizer){
        let point = gesture.locationInView(transitioningController.view)
        let percent = fmaxf(fminf(Float(point.x / 300.0), 0.99), 0.0)
        switch (gesture.state){
        case .Began:
            self.usingGesture = true
            self.transitioningController.navigationController?.popViewControllerAnimated(true)
        case .Changed:
            self.updateInteractiveTransition(CGFloat(percent))
        case .Ended, .Cancelled:
            if percent > 0.5 {
                self.finishInteractiveTransition()
            } else {
                self.cancelInteractiveTransition()
            }
            self.usingGesture = false
        default:
            NSLog("Unhandled state")
        }
    }
}
```

If the user completed more than 50% of the gesture, we call `finishInteractiveTransition()` to finish the transition. If the user swiped less, we decide they've abandoned the transition, and call `cancelInteractiveTransition()`.

Notice that the value is clamped so that it never reaches 1. If the transition ends on 1, the framework has been shown to show an animation glitch.

### Further Reading
* [View Controller Transitions](http://www.objc.io/issue-5/view-controller-transitions.html)
* [Custom Transitions on iOS](http://objectivetoast.com/2014/03/17/custom-transitions-on-ios/)
* [Custom UIViewController Transitions](http://www.teehanlax.com/blog/custom-uiviewcontroller-transitions/)
* [Custom transitions on iOS 7](https://coderwall.com/p/njtb0q)