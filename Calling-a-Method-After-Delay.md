When prototyping or orchestrating animations, it is sometimes useful to run a method after some delay.

### Step 1: Add the delay method

Because the mechanism for using Grand Central Dispatch is a bit ugly, it's useful to wrap it up into a convenient function. Put the function below into each view controller that you want to use it.

```swift
func delay(delay:Double, closure:()->()) {
	dispatch_after(
	    dispatch_time(
	        DISPATCH_TIME_NOW,
	        Int64(delay * Double(NSEC_PER_SEC))
	    ),
	    dispatch_get_main_queue(), closure)
}

```

### Step 2: Use the delay method

To run code after a delay, simply call the method, as below. Since the code in the braces is a closure, you have to use `self` to refer to your class methods and variables.

```swift
// Delay for 2 seconds, then run the code between the braces.
delay(2) {
   self.checkPassword()
}

```