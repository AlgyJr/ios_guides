
UIKit Dynamics was introduced in iOS 7 to introduce a new level of realism to the UI. It's the same physics engine that powers SpriteKit except that it's set up to work with UIViews.

<img src="http://i.imgur.com/hmTooMm.gif" width="250" height="443" />

### Step 1: Create the UIDynamicAnimator

The UIDynamicAnimator is the brains behind the dynamics in the view. There is one instance of UIDynamicAnimator for all the dynamic views, it is the bridge between UIKit and the physics engine, and contains all the "behaviors" such as gravity and collision detection.

The UIDynamicAnimator is instantiated with a reference view, which is usually the root view of the UIViewController, i.e., `self.view`.

```
var animator: UIDynamicAnimator!

override func viewDidLoad() {
{
    super.viewDidLoad()
    animator = UIDynamicAnimator(referenceView: self.view)
}
```

### Step 2: Adding behaviors

You can create nearly any effect by using one or more of the available behaviors: gravity, push, snap, attachment, and collision. After creating the behavior, add it to the animator to enable it.

### Gravity

<img src="http://i.imgur.com/2RAjIi2.gif" width="250" height="443" />

Since you may want to add or remove items from gravity dynamically, you should create the gravity behavior as a property.

```
var gravity: UIGravityBehavior!
```

In `viewDidLoad`, create the gravity behavior and add it to the animator.

```
override func viewDidLoad() {
    super.viewDidLoad()
    gravity = UIGravityBehavior()
    animator.addBehavior(gravity)
}
```

#### Adding/Removing Items from Gravity

At any time, you can add or remove views to the gravity behavior based on user input or some other condition.

```
gravity.addItem(self.greenView)
gravity.removeItem(self.blueView)
```

#### Direction of Gravity

You can change the direction and force of gravity by modifying the `gravityDirection` property.

```
// Gravity still goes down, but at a reduced force
self.gravity.gravityDirection = CGVectorMake(0,0.1)

// Gravity goes up
self.gravity.gravityDirection = CGVectorMake(0,-1)

// Gravity goes diagonal
self.gravity.gravityDirection = CGVectorMake(1,1)
```

### Collision

Since you may want to add or remove items from collision dynamically, you should create the collision behavior as a property.

```
var collision: UICollisionBehavior!
```

In `viewDidLoad`, create the collision behavior and add it to the animator.

```
override func viewDidLoad() {
    super.viewDidLoad()
    collision = UICollisionBehavior()
    animator.addBehavior(collision)
}
```

#### Adding/Removing Items from Collision

At any time, you can add or remove views to the collision behavior based on user input or some other condition.

```
collision.addItem(self.greenView)
collision.removeItem(self.blueView)
```

#### Setting up Boundaries

You can add arbitrary boundaries for objects to collide with. Choose an identifier, so that when a collision event happens, you can check which boundary an object collided with.

```
collision.addBoundaryWithIdentifier("shelf" fromPoint:CGPointMake(0, 200) toPoint:CGPointMake(150, 240))
```

You can set the bounds of the view to automatically be boundaries by setting the `translatesReferenceBoundsIntoBoundaries` property.

```
collision.translatesReferenceBoundsIntoBoundary = true
```

#### Detecting collisions

In order to detect collisions, you have to first set the collisionDelegate.

```
collision.collisionDelegate = self
```

Then, in your view controller, declare that you implement the UICollisionBehaviorDelegate protocol.

```
class ViewController: UIViewController, UICollisionBehaviorDelegate {
...
}
```

If you added the method below previously, be sure to comment it out.
```
collision.translatesReferenceBoundsIntoBoundary = true
```
`var boundary` won't know what the identifier is for that collision.

Implement one of the appropriate collision events, e.g., collision between two objects or collision between an object and a boundary.

For example, to detect the collision of an object with a boundary, implement the following method:

```
func collisionBehavior(behavior: UICollisionBehavior, beganContactForItem item: UIDynamicItem, withBoundaryIdentifier identifier: NSCopying, atPoint p: CGPoint) {

    // You have to convert the identifier to a string
    var boundary = identifier as String

    // The view that collided with the boundary has to be converted to a view
    var view = item as UIView

    if boundary == "shelf" {
        // Detected collision with a boundary called "shelf"
    } else if (boundary == "") {
        // Detected collision with bounds of reference view
    }
}
```

To detect the collision of an object with another object, implement the following method:

```
func collisionBehavior(behavior: UICollisionBehavior, beganContactForItem item1: UIDynamicItem, withItem item2: UIDynamicItem, atPoint p: CGPoint) {
    var view1 = item1 as UIView
    var view2 = item2 as UIView
}
```

### Push

Since you may want to add or remove items from push dynamically, you should create the push behavior as a property.

```
var push: UIPushBehavior!
```

In `viewDidLoad`, create the push behavior and add it to the animator.

```
override func viewDidLoad() {
    super.viewDidLoad()
    push = UIPushBehavior()
    animator.addBehavior(push)
}
```

#### Adding/Removing Items from Push

At any time, you can add or remove views to the push behavior based on user input or some other condition.

```
push.addItem(self.greenView)
push.removeItem(self.blueView)
```

#### Direction of Push

You can change the direction and force of the push force by modifying the `pushDirection` property.

```
// Push goes down, but 1/10 the force of gravity
push.pushDirection = CGVectorMake(0,0.1)

// Push goes up
push.pushDirection = CGVectorMake(0,-1)

// Push goes diagonal
push.pushDirection = CGVectorMake(1,1)
```
