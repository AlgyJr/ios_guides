Configure a simple repeating timer as in the snippet below. After calling the scheduledTimerWithTimeInterval method, the timer is immediately scheduled and will call the function at the time interval.

### Step 1: Declaring the Method

Declare and implement the method that you want to be called when the timer fires. You can name this method whatever you want, just pass the name of the method to the timer when you create it.

```
func onTimer() {
   // Add code to be run periodically	
}

```

### Step 2: Scheduling the Timer

For example, to schedule the `onTimer` method to be called every 5 seconds, use the code below. If you want the timer to fire every 100ms, just use 0.1 for the time interval.

```
NSTimer.scheduledTimerWithTimeInterval(5, target: self, selector: #selector(self.onTimer), userInfo: nil, repeats: true)
```