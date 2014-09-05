# Storyboards and Interface Builder

## Overview

Cocoa has a long history of providing visual tools to design GUIs. While you still need to understand how to build a UI purely with code, visual tools can boost productivity.

The first tool, [released in 1988](http://vimeo.com/62618532) for NeXTSTEP, was Interface Builder (*IB*). You add GUI components-- such as button, labels, and images-- through a drag-and-drop interface. You could then "wire" a button to your view controller's source code, such as having it trigger the `didTapButton` method.

IB saves designs to a `.xib` file (formerly called a "nib" file) which you load at runtime.

```
let toolbarViews = UINib(nibName: "Toolbars", bundle: nil).instantiateWithOwner(self, options:nil)
let myFirstView = toolbarViews[0] as UIView
```

IB to design sets of views that will be reused throughout your app. *Storyboards* are a newer platform, focused on designing entire screens. Storyboards have a better understanding of view controllers and navigation flow, allowing more time-saving features.

For instance, creating a settings screen programmatically results in a lot of boilerplate code. You have to write a long `switch` statement to track each setting in your list, and coordinate that with the cell when it's about to appear on screen. Storyboards have a "Static Cells" option that cuts the work down to a few minutes, and reduces the likelihood of silly bugs. Then call:

```
let settingsViewController = UIStoryboard(name: "Settings", bundle: nil).instantiateInitialViewController() as UITableViewController
```

IB and Storyboards are immensely useful when working with [[autolayout|Autolayout]]. They immediately validate layouts, and warn you if a view has ambiguous constraints. This avoids some trial-and-error when working programatically.

### Disadvantages

As with any productivity tool, visual UI design tools have tradeoffs.

As they emit a large XML file, it can be difficult for a large team to work on a single file. Even if you avoid merge conflicts, you can't visually inspect a diff to see changes.

Localization can be difficult. You'll probably have to write import/export glue code to work with your existing localization system.

One criticism of Storyboards is that a single `.storyboard` file can sprawl out to include dozens of screen, making editing unwieldy. As with code, you expect to refactor your storyboards, splitting them into smaller files that cover specific user stories, such as a single "Login" flow.

Even if you reduce a Storyboard down to a single screen, complex screens may push you to a point where you spend too much time wrestling with tools. Sometimes it's clearer to express intention through code.

Interface Builder and Storyboards are analogous to an agile web framework like Ruby on Rails. They simplify extremely common design patterns, but they are not a panacea. Every website having to scale finds themselves removing the productivity "magic" of agile frameworks.

## Concepts

### Outlets

In your objects, you mark certain properties as *outlets*. They're placeholders. When you load a xib/storyboard, they will be filled in with objects you've wired up in the xib/storyboard.

To setup an outlet, mark them with `iboutlet`. They will then be visible within the layout tool.

```
@IBOutlet weak var avatarView: UIImageView!
```

The property is marked as an implicitly unwrapped optional because the value may be `nil` until you've loaded it up in the xib/storyboard.

While the "IB" in `IBOutlet` stands for Interface Builder, you will it in Storyboards, since it serves the same purpose.

Read more: [Creating an Outlet Connection](https://developer.apple.com/library/ios/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html)

### Actions

You can mark methods in your code as *actions*. You can then wire controls in your xib/storyboard to trigger them.

```
@IBAction func tappedNext(sender: AnyObject) {
  NSLog("Tapped next")
}
```

Like `IBOutlet`, you will see `IBAction` in Storyboards.

Read more: [Creating an Action Connection](https://developer.apple.com/library/ios/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingAction.html)

### File's Owner

In xibs, there is a placeholder object called the *File's Owner.* When your xib is loaded, it will be filled iwth the owner you specified in `instantiateWithOwner`.

### Segues

A single view controller will often contain multiple screens, to cover a single flow. For instance, you may have a `Signup.storyboard`, which covers four screens that make up your signup flow.

You move from one screen to another through a *segue*. You can wire them in your storyboard, or you can call `performSegueWithIdentifier`

```
func userDidTapOnNextButton(sender: AnyObject) {
  self.performSegueWithIdentifier("NameOfSegue", sender:self)
}

```

Read more: [Adding a Segue to a Storyboard](https://developer.apple.com/library/ios/recipes/xcode_help-interface_builder/articles-storyboard/StoryboardSegue.html)


## Troubleshooting

If you rename an outlet in your source code, you must update your xib/storyboard. It is pointing the old name, and it will fail to load if it isn't available. Delete the connection, and reconnect it.

### A Review of the Confusing Parts

* You will often see "nib" when they mean "xib", since that was their old name

## Further Reading

* [Xcode Overview: Building a User Interface](https://developer.apple.com/library/ios/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html)