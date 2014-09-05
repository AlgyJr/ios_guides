# Interface Builder

## Overview

Interface Builder (IB) is a WYSIWYG editor for building user interfaces. You add GUI components-- such as button, labels, and images-- through a drag-and-drop interface. You then "wire" these controls to your view controller's source code. For instance, you can drag a button into your UI, and control-drag it into your source code, to trigger a `didTapButton` method. You load these files at runtime to restore the designs you created.

IB is immensely useful when working with [[autolayout|Autolayout]]. It can immediately validate layouts, and warn you if a view has ambiguous constraints. This avoids some trial-and-error when working programatically.

IB can create two types of files: `.xib` and `.storyboard`.

### Xib

Xibs store sets of views and objects. To load the content of a xib:

```
let toolbarViews = UINib(nibName: "Toolbars", bundle: nil).instantiateWithOwner(self, options:nil)
let myFirstView = toolbarViews[0] as UIView
```

"nib" is not a typo. IB files used to end in `.nib`.

### Storyboard

*Storyboards* are a newer platform than xibs. Storyboards focus on entire screens and user flows. With some heavy lifting, you may be able to do the same thing with a `xib,` but since storyboards understanding how view controllers and navigation fit together, they have even more time-saving features.

For instance, creating a settings screen programmatically results in a lot of boilerplate. You write a long `switch` statement to track each row in your list, and coordinate with a cell dequeuing method when it's about to appear on screen. Storyboards have a "Static Cells" feature, cuts all this work down to minutes.

To load the view controller in your storyboard:

```
let settings = UIStoryboard(name: "Settings", bundle: nil).instantiateInitialViewController() as UITableViewController
```

## Concepts

### Outlets

In your objects, you mark certain properties as *outlets*. They're placeholders. When you load a xib/storyboard, they will be filled in with objects you've wired up in the xib/storyboard.

To setup an outlet, prefix a property with `@IBOutlet`. It will then be available in the editor.

```
@IBOutlet weak var avatarView: UIImageView!
```

The property is marked as an implicitly unwrapped optional because the value may be `nil` until you've loaded up the the xib/storyboard.

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

More: [Creating an Action Connection](https://developer.apple.com/library/ios/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingAction.html)

### File's Owner

xibs contain a placeholder object called the *File's Owner.* When your xib is loaded, it will be filled with the owner you specified in `instantiateWithOwner`. This is usually the object you are connecting outlets and actions to.

### Scenes and Segues

Each screen in a storyboard is known as a *scene*. A storyboard will often contain multiple scenes, covering a user flow. For instance, you may have a `Signup.storyboard`, which covers four screens that make up your signup flow.

You move from one screen to another through a *segue*. You can wire them in your storyboard, or you can call `performSegueWithIdentifier`

```
func userDidTapOnNextButton(sender: AnyObject) {
  self.performSegueWithIdentifier("NameOfSegue", sender:self)
}

```

More: [Adding a Segue to a Storyboard](https://developer.apple.com/library/ios/recipes/xcode_help-interface_builder/articles-storyboard/StoryboardSegue.html)

## Disadvantages

As with any productivity tool, IB has tradeoffs.

As they emit a large XML file, it can be difficult for a large team to work on a single file. Even if you avoid merge conflicts, you can't visually inspect a diff to see changes.

Localization can be difficult. You'll probably have to write import/export glue code to work with your existing localization system.

One criticism of Storyboards is that a single `.storyboard` file can sprawl out to include dozens of screen, making editing unwieldy. As with code, you expect to refactor your storyboards, splitting them into smaller files that cover specific user stories, such as a single "Login" flow.

Even if you reduce a Storyboard down to a single screen, complex screens may push you to a point where you spend too much time wrestling with tools. Sometimes it's clearer to express intention through code.

Interface Builder and Storyboards are analogous to an agile web framework like Ruby on Rails. They simplify extremely common design patterns, but they are not a panacea. Every website having to scale finds themselves removing the productivity "magic" of agile frameworks.

## Troubleshooting

If you rename an outlet in your source code, you must update your xib/storyboard. It is pointing the old name, and it will fail to load if it isn't available. Delete the connection, and reconnect it.

## References

* [Xcode Overview: Building a User Interface](https://developer.apple.com/library/ios/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html)
* [Inteface Builder Help: About Interface Builder](https://developer.apple.com/library/ios/recipes/xcode_help-interface_builder/AboutInterfaceBuilder/AboutInterfaceBuilder.html)