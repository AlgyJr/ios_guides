When creating mocks using Xcode, we can add an extra degree of fidelity by simulating a loading event when we transition to a new screen.

### viewWillAppear and viewDidAppear methods

In the life cycle of a ViewController, the `viewWillAppear` method is called after the `viewDidLoad` method, but right before the view actually "appears". The moment the ViewController actually "appears", the `viewDidAppear` method is called. 

By default, the `viewWillAppear` and `viewDidAppear` methods are not explicitly added to the Swift ViewController file; Unless otherwise specified, they just get called in the background with no special instructions.

Unlike the `viewDidLoad` method, the `viewWillAppear` and `viewDidAppear` are called every the ViewController is transitioned to. This makes the `viewWillAppear` a great place to setup the initial starting positions of animations and the `viewDidAppear` the perfect place to execute the animation.  

## Use Case 1: Simulate Screen Loading 

### Step 1: Add Common Swift File

We have created a delay method fro you to use, download this [Common.swift](https://www.dropbox.com/s/mzfmjlvv863x95e/Common.swift?dl=0) file and add it to your project. 

Alternatively, you can copy and paste the delay function directly in your Swift ViewController file [Add the Delay Method](https://github.com/codepath/ios_guides/wiki/Calling-a-Method-After-Delay#step-1-add-the-delay-method)
 
### Step 2: Add/Configure Activity Indicator
It's super easy to take any ViewController you already have setup and incorporate simulated loading.

- In the case that you have a ScrollView with an "Feed" ImageView, nest the Activity Indicator inside the ScrollView alongside the "Feed" ImageView. [UIActivityIndicatorView](https://github.com/codepath/ios_guides/wiki/Using-UIActivityIndicatorView), [Nest the Child View](https://github.com/codepath/ios_guides/wiki/Creating-Nested-Views#step-2-nest-the-child-views)  
![Activity Doc Outline](http://i.imgur.com/TVB7y4G.png) 

- Make sure you select, **Hides When Stopped** in The Activity Indicator Attributes Inspector.  
![Hides When Stopped](http://i.imgur.com/ib87r65.png)  

### Step 3: Setup viewWillAppear

Just before the ViewController "appears", hide the "Feed" ImageView, and start animating the Activity Indicator.

```swift
// Right before the ViewController "appears"...
override func viewWillAppear(animated: Bool) {
   // hide feed view initially
   feedImageView.hidden = true       
   // turn on the activity indicator
   loadingIndicator.startAnimating()
    }
```

### Step 4: Setup viewDidAppear

When the ViewController finally does "appear", delay for 2 seconds before stopping the Activity Indicator animation and showing the "Feed" ImageView.

```swift
// The moment the ViewController "appears"...
override func viewDidAppear(animated: Bool) {
   // Delay for 2 seconds before...    
   delay(2) { () -> () in
   // hide feed view initially
   self.feedImageView.hidden = false   
   // turn on the activity indicator
   self.loadingIndicator.stopAnimating()     
   }
}
```

**NOTE** the variables within the delay closure require, `self.`, but don't worry, if you forget Xcode will let you know and offer to fix the issue :)  
