Many UIView properties can be animated, including:

- frame
- center
- backgroundColor
- alpha
- transform
   - rotation
   - scale
   - translation

### Step 1: Create an animation block

In order to animate one or more views, simply create an animation block. The animation block will animate from the current value to the value that's set within the block. You can call multiple views within the block.

```swift
// Optionally initialize the property to a desired starting value
self.firstView.alpha = 0
self.secondView.alpha = 1
UIView.animateWithDuration(0.4, animations: {
    // This causes first view to fade in and second view to fade out
    self.firstView.alpha = 1
    self.secondView.alpha = 0
})
        
```

### Animation Options:

![Animation Options gif](http://i.imgur.com/KJ8QWAv.gif)

You can add Animation Options to further customize your animation. To add multiple Animation Options, surround your Animation Options with `[ ]`, like `[UIViewAnimationOptions.Autoreverse, UIViewAnimationOptions.Repeat]`.

```swift
UIView.animateWithDuration(0.8, delay: 0.0,  
   // Autoreverse runs the animation backwards and Repeat cycles the animation indefinitely. 
   options: [UIViewAnimationOptions.Autoreverse,
      UIViewAnimationOptions.Repeat], animations: { () -> Void in
   self.bubbleImageView.transform = CGAffineTransformMakeTranslation(0, 10)
   }, completion: nil)
```

If you **DO NOT** want any animation options, you can plug in `[]` for the `options:` value.

```swift
UIView.animateWithDuration(0.4, delay: 0.0,   
   options: [], animations: { () -> Void in
   self.imageView.transform = CGAffineTransformMakeScale(1.5, 1.5)
   }, completion: nil)
```

### Spring Animation

![spring animation gif](http://i.imgur.com/kkG0GXf.gif) 
  
Sometimes you want a little spring action to make your animations seem a little more alive and less stiff. This example applies a subtle spring animation to a slide out tray.

The `usingSpringWithDamping` parameter can be set with a value between 0 and 1. A setting closer to 1 decreases the amount of "springyness". A value of 0.4 to 0.5 seems to create a nice subtle springy effect, but you should experiment with different values to see what works for your particular situation.

```swift
UIView.animateWithDuration(0.4, delay: 0, usingSpringWithDamping: 0.5, initialSpringVelocity: 1, options:[] , animations: { () -> Void in
   self.trayView.center = self.trayDown
   }, completion: { (Bool) -> Void in
})
``` 