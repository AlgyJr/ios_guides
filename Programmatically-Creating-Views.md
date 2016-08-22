It is sometimes necessary to programatically create views. Follow the steps below to create views.

### Step 1: Creating the View

First, decide where you want the view to be and how large you want it to be. Create a frame with the desired dimensions and use that frame to create the view. After you create the view, configure any properties that you want.

```
var frame = CGRect(x: 0, y: 0, width: 100, height: 100)
var blueView = UIView(frame: frame)
blueView.backgroundColor = UIColor.blueColor()

```

For example, to create an image view, do the following:

```
var frame = CGRect(x: 0, y: 0, width: 100, height: 100)
var imageView = UIImageView(frame: frame)
imageView.image = UIImage(named: "smiley_face")
imageView.userInteractionEnabled = true

```

### Step 2: Adding the View

Next, you have to add the view into another view. This view can be the main view or any other view in the hierarchy. Its up to you where you want to embed the newly created view.

```
view.addSubview(blueView)

```

Adding the subview as above will add the subview on top of the other subviews. You can also insert the subview at an arbitrary z-index.

```
view.insertSubview(blueView, atIndex:0)

```
