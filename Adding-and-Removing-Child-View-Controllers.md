Adding and removing child view controllers ensures that the parent view controller knows of its children. This will help when doing things like calling a modal from a child view that has been added to the parent. This behavior can be buggy if the parent doesn't know that it's connected to the child. Another reason is so that `viewWillAppear`, `viewDidAppear`, `viewWillDisappear`, and `viewDidDisappear` work consistently.

### Adding a child view controller

```swift
func displayContentController(content: UIViewController) {
    addChildViewController(content)
    self.view.addSubview(content.view)
    content.didMoveToParentViewController(self)
}

```

### Removing a child view controller

```swift
func hideContentController(content: UIViewController) {
    content.willMoveToParentViewController(nil)
    content.view.removeFromSuperview()
    content.removeFromParentViewController()
}
```