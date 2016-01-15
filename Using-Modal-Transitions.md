Any view controller may present another view controller with some animation, commonly sliding up from below or fading in. Of course, as in Inception, that view controller can present another view controller which can present another view controller and so on. However, the common use case is to present a single view controller that will be dismissed shortly, as depicted below.

<img src="http://i.imgur.com/4wESHoK.gif" width="250" height="443" />&nbsp;&nbsp;<img src="http://i.imgur.com/KpX7FcB.gif" width="250" height="443" />

This is a quick guide to presenting and dismissing view controllers in Storyboard.

### Step 1: Create the Segue

Ctrl-drag from the button to the view controller that you want to present modally, as show below.

![Create Segue|700](http://i.imgur.com/HjGJq93.gif)

### Step 2: Configure the Segue Animation Properties

Click on the segue in the Storyboard and open the Properties view to choose the animation type or disable animation.

The presentation style of a view controller governs its appearance onscreen. Full screen presentation style covers the entire screen. The PageSheet & FormSheet styles allow portions of the underlying view controller to show through.

The standard transition styles for presenting the view controller can be supplemented with custom transitions of your own.

![Configure Properties|700](http://i.imgur.com/HXCQTz5.gif)

## Other Tasks

### Triggering the Transition Manually

Sometimes, you want the transition to happen, but it's not the result of a button tap. In that case, you have to name the segue and then call the `performSegue` method in code. The name of the segue doesn't matter, but it should be unique for that view controller.

![Name Segue|700](http://i.imgur.com/m1SnyFw.gif)

Once, you've given the segue a unique name, you can call invoke it in code, which will trigger the modal transition.

```
performSegueWithIdentifier("firstSegue", sender: self)

```

### Dismissing Modal Transitions

The easiest way to dismiss a modal transition and return to the original view controller is to do it in code. Create a button that will dismiss the view controller and add a button action.

In the button action, call the method below to dismiss the current view controller.

```
dismissViewControllerAnimated(true, completion: nil)

```

### Passing Data

Often, you will need to pass data from a view controller to the view controller that is being presented. There is a special method that is called right before the segue happens. In that method, you can access the view controller that is being transitioned to and set the appropriate property.

In the source view controller, implement the following method. Often, you'll want to cast the destination view controller to the specific view controller so you can set the appropriate property.

```
override func prepareForSegue(segue: UIStoryboardSegue!, sender: AnyObject!) {
	var destinationViewController = segue.destinationViewController as PhotoViewController

	destinationViewController.image = self.imageView.image

}

```

This assumes that the destination view controller has a property called `image`.

```
class PhotoViewController : UIViewController {
	var image: UIImage!

}

```

Then, in the destination view controller, after the view loads, set the image property of the image view.

```
override func viewDidLoad() {
	super.viewDidLoad()

	imageView.image = image
}

```

