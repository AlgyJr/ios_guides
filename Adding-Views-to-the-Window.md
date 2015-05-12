All views in iOS belong to a window. A window is just a view with a few special properties. Normally, iOS applications only have a single window. It is occasionally useful to add views directly to a window or even create multiple windows.

You can add a view to the window directly if you want to easily cover all other views, e.g., the navigation bar and tab bar. For example, you might implement a custom alert view or action sheet by adding a view to the window.

If you create a new window (this is rare), you can also choose its level, which can be used to cover the status bar, alert views, or the keyboard.

### Step 1: Getting the Key Window

To retrieve the window that is created for you in Storyboard or in the app delegate, do the following:

```
var window = UIApplication.sharedApplication().keyWindow

```

### Step 2: Adding a View

The coordinate (0,0) of the window is at the top left of the phone. Its sometimes useful to get the coordinate of an existing view in the coordinate system of the window. That will account for things like the status bar and the navigation bar.

For example, if there is an image view embedded in some other view, you can get its frame in the coordinate system of the window by doing the following:

```
var frame = window.convertRect(imageView.frame, fromView: containerView)

```

In the example above, the imageView is a subview of containerView. The new frame variable is the coordinate of the imageView if it were a subview of the window.

To add a view to the window, simply use the add or insert subview methods.

```
window.addSubview(imageView)

```