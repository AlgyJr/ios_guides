You can choose whether to display as an alert or action sheet style. 

## Using Alert style

The alert style presents modally in the center of their presenting view controllers. 

![Alert|250](https://i.imgur.com/cMAZh9H.gif)

### Step 1: Create the UIAlertController

Create the alert controller as below, setting preferredStyle to Alert

```swift
let alertController = UIAlertController(title: "Title", message: "Message", preferredStyle: .alert)
```

```objc
UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"Title"
                                                                           message:@"Message"
                                                                    preferredStyle:(UIAlertControllerStyleAlert)];
```

### Step 2: Add buttons

Create a UIAlertAction for each button to display and respond to.

```swift
// create a cancel action
let cancelAction = UIAlertAction(title: "Cancel", style: .cancel) { (action) in
    // handle cancel response here. Doing nothing will dismiss the view.
}
// add the cancel action to the alertController
alertController.addAction(cancelAction)

// create an OK action
let OKAction = UIAlertAction(title: "OK", style: .default) { (action) in
    // handle response here.
}
// add the OK action to the alert controller
alertController.addAction(OKAction)
```
```objc
// create a cancel action
UIAlertAction *cancelAction = [UIAlertAction actionWithTitle:@"Cancel"
                                                    style:UIAlertActionStyleCancel
                                                  handler:^(UIAlertAction * _Nonnull action) {
                                                         // handle cancel response here. Doing nothing will dismiss the view.
                                                  }];
// add the cancel action to the alertController
[alert addAction:cancelAction];

// create an OK action
UIAlertAction *okAction = [UIAlertAction actionWithTitle:@"OK"
                                                   style:UIAlertActionStyleDefault
                                                 handler:^(UIAlertAction * _Nonnull action) {
                                                         // handle response here.
                                                 }];
// add the OK action to the alert controller
[alert addAction:okAction];
```

Adding two buttons will place them side by side in the alert. Adding more than two buttons stacks them in the view similar to the ActionSheet style.


### Step 3: Show the UIAlertController

```swift
present(alertController, animated: true) {
    // optional code for what happens after the alert controller has finished presenting
}
```
```objc
[self presentViewController:alert animated:YES completion:^{
    // optional code for what happens after the alert controller has finished presenting
}];
```
If you wish to call `present()` outside a view controller, you would do:

```swift
UIApplication.shared.keyWindow?.rootViewController?.present(alertController, animated: true) {
    // optional code for what happens after the alert controller has finished presenting
}
```
```objc
[[UIApplication sharedApplication].keyWindow.rootViewController presentViewController:alert
                                                                             animated:YES
                                                                           completion:^{
                                                                                   // optional code for what happens after the alert controller has finished presenting
                                                                           }];
```

## Using Action Sheets style

The action sheet style anchors the view to the bottom of the presenting view controller.

![Action|250](https://i.imgur.com/8JfoPh3.gif)

### Step 1: Create the UIAlertController

Create the alert controller as below, setting preferredStyle to ActionSheet

```swift
let alertController = UIAlertController(title: "Title", message: "Message", preferredStyle: .actionSheet)
```

### Step 2: Add buttons

Create a UIAlertAction for each button to display and respond to. The destructive style options shows the button text in red.

```swift
let logoutAction = UIAlertAction(title: "Log Out", style: .destructive) { (action) in
    // handle case of user logging out
}
// add the logout action to the alert controller
alertController.addAction(logoutAction)     

let cancelAction = UIAlertAction(title: "Cancel", style: .cancel) { (action) in
    // handle case of user canceling. Doing nothing will dismiss the view.
}
// add the cancel action to the alert controller
alertController.addAction(cancelAction)
```

### Step 3: Show the UIAlertController

```swift
present(alertController, animated: true) {
    // optional code for what happens after the alert controller has finished presenting
}
```
