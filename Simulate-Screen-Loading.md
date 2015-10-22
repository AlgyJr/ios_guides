When creating mocks using Xcode, we can add an extra degree of fidelity by simulating a loading event when we transition to a new screen.

### viewWillAppear and viewDidAppear methods

In the life cycle of a ViewController, the `viewWillAppear` method is called after the `viewDidLoad` method, but right before the view actually "appears". The moment the ViewController actually "appears", the `viewDidAppear` method is called. 

By default, the `viewWillAppear` and `viewDidAppear` methods are not explicitly added to the Swift ViewController file; Unless otherwise specified, they just get called in the background with no special instructions.

Unlike the `viewDidLoad` method, the `viewWillAppear` and `viewDidAppear` are called every the ViewController is transitioned to. This makes the `viewWillAppear` a great place to setup the initial starting positions of animations and the `viewDidAppear` the perfect place to execute the animation.

## Use Case 1: Simulate Screen Loading 
