Many UIView properties can be animated, including:

- frame
- center
- backgroundColor
- alpha
- transform

### Step 1: Create an animation block

In order to animate one or more views, simply create an animation block. The animation block will animate from the current value to the value that's set within the block. You can call multiple views within the block.

```
// Optionally initialize the property to a desired starting value
self.firstView.alpha = 0
self.secondView.alpha = 1
UIView.animateWithDuration(0.4, animations: {
    // This causes first view to fade in and second view to fade out
    self.firstView.alpha = 1
    self.secondView.alpha = 0
})
        
```