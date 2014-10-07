## Introduction

iOS was built from the ground up to support animation. It is absolutely required to maintain the illusion of a touch-based interface.

It's best to start out using Springs-and-Struts layout. Later in the guide we'll cover Auto-Layout, with requires a little more abstract thinking.

## Simple Animation with UIKit

Most of the time, you will perform animation by working directly with UIKit. Say you have a view, `myView`, and you want to animate its movement from its current position to a new position, the `myNewFrame` rect. You simply call:

```
UIView.animateWithDuration(0.35) {
    self.myView.frame = myNewFrame
}
```

Behind the scenes, it is making calls to a lower layer in the stack, **Core Animation.** You work with Core Animation directly when you need to create complex animation, or do something performance intensive.

There are more complex animation methods available to avoid going lower. If you need to call code with the animation completes:

```
UIView.animateWithDuration(0.35, animations: {
    self.myView.frame = myNewFrame
}, completion: { (finished) in
    NSLog("Animation completed.")
})
```

If you want to pass even more options, such as the animation curve:

```
UIView.animateWithDuration(0.35, delay: 0.5, options: .CurveEaseInOut, animations: {
  self.myView.frame = myNewFrame
}, completion: { (finished) in
  NSLog("Animation completed.")
})
```

This style of animation, where you just specify new attributes and let Core Animation figure it out for you is **implicit** animation. To create complex animation, you need to delve directly into layers.

## Understanding Layers

Core Animation is a misnomer for the framework. Its original name was *LayerKit.* It is a framework for displaying a hierarchy of layers on screen.

A layer is a container for a bitmap image. Layers are rendered on screen via the GPU, which makes it super fast to render most animation.

UIKit's view hierarchies are backed by a near-identical layer hierarchy. In fact, every UIView has a `.layer` property for accessing its corresponding layer. Behind the scenes, UIView populates layer's contents for you. So while a UILabel displays text, it is ultimately flattened to a bitmap and handed over to its associated layer.

Views are are so tightly associated to views that many attributes are shared; for instance, if you change the layer's `opacity` value, then the view's `alpha` will also change.

The big difference between a view and a layer is a view can receive events. If you don't need the overhead, you can managed your layer's sublayers yourself. This might improve performance if you're dealing with hundreds of layers on screen at once, such as if you're building a particle system. Most of the time, this is overkill.

### Implicit Animation in Layers

When layers exist by themselves, not attached to a view, most property changes are animated by default. However, layers attached to views have this default animation turned off. When you call `UIView`'s `animatedWithDuration`, implicit animation is turned on again.

### Transactions

All animation changes are bundled together in a transaction. This is usually invisible, as there's usually one created for you with every pass through the run loop. This lets you make a bunch of changes that are batched together at once in the end.

Imagine you make two changes to your layer:

```
myLayer.opacity = 0.0
myLayer.opacity = 1.0
```

The first change has no effect. There is no momentary "blip" of 0.0 opacity. Instead, after all your code is run, Core Animation looks at all the final values to create the animation.

## Explicit Animation

An explicit animation allows you to construct more complex animations, such as animating a view animate along a bezier path.

To construct a basic animation:

```
myView.layer.position = endPosition
let animation = CABasicAnimation(keyPath: "position")
animation.fromValue = NSValue(CGPoint:startPosition)
animation.toValue = NSValue(CGPoint:endPosition)
animation.duration = 1.0
myView.layer.addAnimation(animation, forKey: "MyAnimationKey")
```

The `position` key is the name of the property you want animated. The key in the `addAnimation` method is a made-up key you will use to identify the animation in flight.

One caveat from the above example is that we made sure to update the underlying `layer.position` value in addition to applying the animation. Otherwise, the animation would have completed and then the layer would have immediately jumped back to initial position. 

## References

* [Cocoa Application Competencies for iOS: Animation](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoaapp/Animation.html)