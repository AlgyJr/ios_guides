Many UIView properties can be animated, including:

- frame
- center
- backgroundColor
- alpha
- transform

### Step 1: Create an animation block

In order to animate one or more views, simply create an animation block. The animation block will animate from the current value to the value that's set within the block. You can call multiple views within the block.

```Swift
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

```Swift
UIView.animateWithDuration(0.8, delay: 0.0,  
   // Autoreverse runs the animation backwards and Repeat cycles the animation indefinitely. 
   options: [UIViewAnimationOptions.Autoreverse,
      UIViewAnimationOptions.Repeat], animations: { () -> Void in
   self.bubbleImageView.transform = CGAffineTransformMakeTranslation(0, 10)
   }, completion: nil)
```