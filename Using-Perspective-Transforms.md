View transforms allow you to scale, rotate, and translate a view, but its also interesting to be able to do perspective transforms. In order to do that, you have to use the underlying layer property.

### Step 1: Define the Transform

Create the CATransform3D and set the m34 property. The m34 property controls the amount of foreshortening. The code snippet below uses 500. If you increase that number, you'll get less foreshortening. Try values of 2000 or 250.

```
var transform = CATransform3DIdentity;
transform.m34 = 1.0 / 500.0;

```

Then, decide what kind of transform you want. You can rotate around an arbitrary 3D vector, although it's common to rotate around one axis at a time.

#### Example: Rotate 45 degrees around the y-axis

```
transform = CATransform3DRotate(transform, CGFloat(45 * M_PI / 180), 0, 1, 0)

```

#### Example: Rotate 10 degrees around the x-axis

```
transform = CATransform3DRotate(transform, CGFloat(10 * M_PI / 180), 1, 0, 0)

```

### Step 2: Set the Transform

Set the transform on the layer of the view. In the example below, assume that you want to rotate an image view using the transform defined in Step 1.

```
imageView.layer.transform = transform

```

### Step 3: Setting the anchor point
The anchor point is where your rotations will happen around. It always starts out as `CGPoint(x: 0.5, y: 0.5)` which is the center of the view. That's why when you rotate a regular view, it rotates about its center. You can change this though using:

```
func setAnchorPoint(anchorPoint: CGPoint, forView view: UIView) {
    var newPoint = CGPointMake(view.bounds.size.width * anchorPoint.x, view.bounds.size.height * anchorPoint.y)
    var oldPoint = CGPointMake(view.bounds.size.width * view.layer.anchorPoint.x, view.bounds.size.height * view.layer.anchorPoint.y)
    
    newPoint = CGPointApplyAffineTransform(newPoint, view.transform)
    oldPoint = CGPointApplyAffineTransform(oldPoint, view.transform)
    
    var position = view.layer.position
    position.x -= oldPoint.x
    position.x += newPoint.x
    
    position.y -= oldPoint.y
    position.y += newPoint.y
    
    view.layer.position = position
    view.layer.anchorPoint = anchorPoint
}
```
The above function resets the position and anchor point so that you are only setting the anchor point.

Then in your `viewDidLoad` function you can change the anchor point of your view

```
setAnchorPoint(CGPoint(x: 1.0, y: 0.5), forView: feedView)

```

Now when you rotate your feedView, it will rotate about the far right edge (x: 1.0) and center y.

### Step 4: Animating the transform

If you want to animate the transform, you have to use Core Animation. In the example below, we animate the transform on the image view.

```
var animation = CABasicAnimation(keyPath: "transform")
animation.toValue = NSValue(CATransform3D:transform)
animation.duration = 3

imageView.layer.addAnimation(animation, forKey: "transform")

```


