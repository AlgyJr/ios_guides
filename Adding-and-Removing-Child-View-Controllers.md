Adding and removing child view controllers ensures that the parent view controller knows of its children. This will help when doing things like calling a modal from a child view that has been added to the parent. This behavior can be buggy if the parent doesn't know that it's connected to the child. Another reason is so that `viewWillAppear`, `viewDidAppear`, `viewWillDisappear`, and `viewDidDisappear` work consistently.

### Adding a child view controller

```swift
addChildViewController(yourViewController)
view.addSubview(yourViewController.view)
yourViewController.didMove(toParentViewController: self)
```

#### Example function

```swift
func displayContentController(content: UIViewController) {
    addChildViewController(content)
    self.view.addSubview(content.view)
    content.didMove(toParentViewController: self)
}
```

### Removing a child view controller

```swift
yourViewController.willMove(toParentViewController: nil)
yourViewController.view.removeFromSuperview()
yourViewController.removeFromParentViewController()
```

#### Example function

```swift
func hideContentController(content: UIViewController) {
    content.willMove(toParentViewController: nil)
    content.view.removeFromSuperview()
    content.removeFromParentViewController()
}
```