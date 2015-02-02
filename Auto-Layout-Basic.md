This guide provides a quick overview and basic examples of the most
common uses cases for using Auto Layout in Interface Builder.  A guide
on some more advanced features of Auto Layout including how to work with
Auto Layout programmatically can be found
[here][advancedautolayoutguide].  Another good resource is Apple's
[official Auto Layout guide][appleautolayoutguide].

<!--- TODO: insert link to advanced guide here -->

## What is Auto Layout and why use it?
Until the introduction of iPad and iPhone 5, all screens in the iOS
world had the same [point][pointsvspixels] dimensions of 320x480 height.
At this point in time it was often possible to describe an app's layout
by specifying the absolute position and size of views.

These days, most applications will want their layout to be responsive to
changes the screen size or the content they are displaying.  _Auto Layout_
provides a convenient way for you to describe how the size and position
of your [views][viewsguide] should change when the size and position
their parent views or neighboring views change.  This can happen for
example when:

* your application is run on different devices
* the size of or number of neighboring views change to reflect a change
  in the content the application is displaying
* the user rotates the orientation of the device

In Auto Layout you describe a view's layout by providing one or more
_constraints_ that describe how it is size and position are related to
the size and position of other views.

[appleautolayoutguide]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/Introduction/Introduction.html
[viewsguide]: https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/Introduction/Introduction.html
[pointsvspixels]: https://developer.apple.com/library/ios/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/GraphicsDrawingOverview/GraphicsDrawingOverview.html#//apple_ref/doc/uid/TP40010156-CH14-SW7

### Example of layout problem
To get a sense of the kind of problem that arises when laying out views
and that Auto Layout can solve for us, consider the following example.
Using Interface Builder we've added a single view (colored red) inside
the view controller's root view.

<a href="http://imgur.com/ZAuzNug"><img src="http://i.imgur.com/ZAuzNug.png" title="source: imgur.com" /></a>

Without adding any layout constraints here is what this interface looks
likes in some situations where this app might run.

<a href="http://imgur.com/0WwqszZ"><img src="http://i.imgur.com/0WwqszZ.png" title="source: imgur.com" /></a>

Depending on the use case, chances are this is not behavior that we
want.  For example, if the red view were meant to be a button that is
always pinned to the upper right corner of the screen, the behavior is
incorrect for the landscape and iPhone 6 sizes.

## Basic constraints
In Auto Layout, you describe your application's layout by adding
constraints that define relationships between the size and position its
views.  In order for Auto Layout to function properly you'll need to
provide enough constraints for each view so that the system can
determine its size (width and height) and location (the x and y
coordinates of the top left corner of the view).

You can add constraints in Interface Builder by ...

<!--- TODO: show how to add constraints in interface builder -->

All or nothing
You'll need to provide enough

Here are some the most common situations you'll come across.

### Pinning to one or more edges
- interface button
You'll often want to fine
- by default to relative to closest view but can select based on
  dropdown

### Resize with parent view
- alert view

### Center a view within a parent view
- logo in spash screen / sign in screen

## Working with constraints in Interface Builder

### Editing constraints
- constraints are additive

### Constraint Errors and Warnings

#### Misplaced Views
##### update frames
  - don't update frames when underconstrained
  - fairly common

##### update constraints
   - can change your constraints in unexpected ways
   - don't update constraints when overconstrained since this may end up
  creating duplicate constraints

#### Conflicting Constraints
- overconstrained

#### Missing Constraints
- underconstrained
- be careful when adding constraints as it may not add the one you want

## Dealing with flexible content size

### Intrinsic content size of a view

### Two labels: one on top of another

### Left aligned label next to right aligned label
#### Compression resistance

### Left aligned multiline label next to right aligned label
- inequality constraint
#### Content hugging

## Manipulating constraints programmatically
## VFL

### Understanding debug output

## Other topics
### Springs and Struts and AutoResizing Mask

- autolayout is per interface builder document:
- http://stackoverflow.com/questions/9370072/xcode-4-3-not-presenting-autoresizing-panel-in-size-inspector
- Tip: If you want to remove all constraints and start over, choose Issues > Clear Constraints.
