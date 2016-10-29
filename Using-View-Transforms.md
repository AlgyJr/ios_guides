
There are 3 types of 2 dimensional transforms that you can apply to views: translation, scale, and rotation. You can also use the view transform property to set a 3D perspective transform.

### Applying a Scale Transform

To apply a scale transform, create it as below. In this example, the view will scale up by 2 times.

#### Initialize a scale transform

```swift
view.transform = CGAffineTransform(scaleX: 1.5, y: 1.5)
```

#### Update the scale from an existing transform

```swift
view.transform = view.transform.scaledBy(x: 0.5, y: 0.5)
```

### Applying a Rotation Transform

To apply a rotation transform, create it as below. Since the units are in radians, you will need to convert from degrees. In this example, the view will rotate by 45 degrees. You also need to convert it to be of type CGFloat, which is the original Core Graphics type.

#### Initialize a rotation transform

```swift
view.transform = CGAffineTransform(rotationAngle: CGFloat(45 * M_PI / 180))

or in Swift 3 syntax:
view.transform = view.transform.rotated(by:rotation)
```

#### Update the rotation from an existing transform

```swift
view.transform = view.transform.rotated(by angle: CGFloat(45 * M_PI / 180))
```

### Applying a Translation Transform

To apply a translation transform, create it as below. In this example, the view will offset by 50 pixels in both the X and Y axis.

#### Initialize a translation transform

```swift
view.transform = CGAffineTransform(translationX: 50, y: 50)
```

#### Update the translation from an existing transform

```swift
view.transform = view.transform.translatedBy(x: 10, y: 10)
```
### Combining Transforms

In the examples above, we were applying a single transform to a view. Sometimes, you want to be able to apply multiple transforms simultaneously. Each of the transform methods above has another method that will take an existing transform and additively apply a new transform.

For example, to scale and rotate, you can first scale it, then add the rotation transform (or vice versa)

```swift
view.transform = CGAffineTransform(scaleX: 1.5, y: 1.5)
view.transform = view.transform.rotated(by angle: CGFloat(45 * M_PI / 180))

```

### Clearing Transforms with Identity
An easy way to remove any transforms applied is to simply set the transform to the Identity or no transform state.
For example,

```swift
view.transform = CGAffineTransform.identity
```
will remove any previous transformations and the view will appear as it had before.
