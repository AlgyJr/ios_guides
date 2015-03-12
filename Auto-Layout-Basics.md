<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
- [What is Auto Layout and why use it?](#what-is-auto-layout-and-why-use-it)
  - [Example of layout problem](#example-of-layout-problem)
- [Basic constraints](#basic-constraints)
  - [Pinning to one or more edges](#pinning-to-one-or-more-edges)
  - [Resize with parent view](#resize-with-parent-view)
  - [Center a view within a parent view](#center-a-view-within-a-parent-view)
- [Working with constraints in Interface Builder](#working-with-constraints-in-interface-builder)
  - [Specifying the second view to which a constraint should be relative](#specifying-the-second-view-to-which-a-constraint-should-be-relative)
  - [Editing constraints](#editing-constraints)
  - [Constraint Errors and Warnings](#constraint-errors-and-warnings)
    - [Misplaced Views](#misplaced-views)
      - [Update frames](#update-frames)
      - [Update constraints](#update-constraints)
    - [Conflicting Constraints](#conflicting-constraints)
    - [Missing Constraints](#missing-constraints)
- [Dealing with flexible content size](#dealing-with-flexible-content-size)
  - [Intrinsic content size of a view](#intrinsic-content-size-of-a-view)
  - [Two labels: one on top of another](#two-labels-one-on-top-of-another)
  - [Left aligned label next to right aligned label](#left-aligned-label-next-to-right-aligned-label)
    - [Compression resistance](#compression-resistance)
  - [Left aligned multiline label next to right aligned label](#left-aligned-multiline-label-next-to-right-aligned-label)
    - [Content hugging](#content-hugging)
- [Manipulating constraints programmatically](#manipulating-constraints-programmatically)
- [VFL](#vfl)
  - [Understanding debug output](#understanding-debug-output)
- [Other topics](#other-topics)
  - [Springs and Struts and AutoResizing Mask](#springs-and-struts-and-autoresizing-mask)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

This guide provides a quick overview and basic examples of the most
common uses cases for using Auto Layout in Interface Builder.  A guide
on some more advanced features of Auto Layout including how to work with
Auto Layout programmatically can be found
[here](advancedautolayoutguide).  Another good resource is Apple's
[official Auto Layout guide][appleautolayoutguide].

<!--- TODO: insert link to advanced guide here -->

## What is Auto Layout and why use it?
Until the introduction of iPad and iPhone 5, all screens in the iOS
world had the same [point][pointsvspixels] dimensions of 320x480 width x
height.  At this point in time it was often possible to describe an
app's layout by specifying the absolute position and size of views.

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
views.  __In order for Auto Layout to function properly you'll need to
provide enough constraints for each view so that the system can
determine its size (width and height) and location (the x and y
coordinates of the top left corner of the view).__

You can add constraints in Interface Builder by selecting one or more
views and using the the Auto Layout controls (boxed in red below).  The
buttons are from left to right:

1. Aligntool - specify how views should be aligned relative to each
   other
2. Pin tool - specify that a view should be a fixed distance from
   another view
3. Issues tool - quick fixes for [errors or warnings](#constraint-errors-and-warnings) that may come up as you
add constraints.
4. Resizing settings - changes the way Interface Builder updates
   constraints when you resize views.  You probably won't use this.

<a href="http://imgur.com/KZsEwNc"><img src="http://i.imgur.com/KZsEwNc.png" title="source: imgur.com" /></a>

You can also add constraints by control-dragging from one view to the
relevant area of another view and selecting the appropriate item in the
context menu.

<a href="http://imgur.com/Z9t4JEM"><img src="http://i.imgur.com/Z9t4JEM.gif" title="source: imgur.com" /></a>

Here are some of the most basic situations you'll come across.

### Pinning to one or more edges
You'll often find yourself wanting to position a view to be a fixed
distance from an edge of its parent view or one of its neighboring
views.

Here we pin the the red view to the top right corner of its parent view.
This might be useful for example if the red view is a menu button that
we want to always be accessible from the same location.  Notice that we
had to specify the height and width of the view as well so that Auto
Layout would have enough informatin to figure out both the location and
size of the red view.

<a href="http://imgur.com/TDwD12X"><img src="http://i.imgur.com/TDwD12X.gif" title="source: imgur.com" /></a>

The result when we run our app is as follows.

<a href="http://imgur.com/Ewh64iM"><img src="http://i.imgur.com/Ewh64iM.png" title="source: imgur.com" /></a>

### Resize with parent view
Another common situation is for a view to resize its dimensions (either
height, width or both) to match the parent view's dimensions.

Here we specify that the red view should be pinned to the top of the
screen with fixed height, but it should resize to span the width of the
screen.  This might be useful for example in a situation where the red
view contains an alert message.

<a href="http://imgur.com/YVs4hFP"><img src="http://i.imgur.com/YVs4hFP.gif" title="source: imgur.com" /></a>

Here's what the result is when running our app:

<a href="http://imgur.com/UUcCSJH"><img src="http://i.imgur.com/UUcCSJH.png" title="source: imgur.com" /></a>

### Center a view within a parent view
Sometimes you'll want to center a view (either vertically, horizontally,
or both) within another view.

Here we center the red view vertically on the screen.  This might be
useful if we need to show our logo in the Launch Screen.  Notice that we
had select "Update Frames" after setting our constraints.  More on this
[below].

<a href="http://imgur.com/VfQLiGI"><img src="http://i.imgur.com/VfQLiGI.gif" title="source: imgur.com" /></a>

## Working with constraints in Interface Builder
Here are a few common situations that will come up as you add and modify
constraints in Interface Builder

### Specifying the second view to which a constraint should be relative
The pin tool by default will try to create a constraint relative to the
nearest neighbor.  You can change which view a constraint is relative to
by clicking on the small arrow and selecting the right view in the drop
down menu.

<a href="http://imgur.com/CufGkhG"><img src="http://i.imgur.com/CufGkhG.png" title="source: imgur.com" /></a>

### Editing constraints

Note that constraints added using the align and pin buttons are
additive.  They do not update existing constraints, but rather create
entirely new ones.  For example, here we try to set a second height
constraint which results in a case of [conflicting
constraints](#conflicting-constraints).

<a href="http://imgur.com/APMxBUm"><img src="http://i.imgur.com/APMxBUm.gif" title="source: imgur.com" /></a>

You can edit an constraint by selecting the view associated with that
constraint and using the Size inspector, or simply selecting the
constraint directly in the Scene Outline.

<a href="http://imgur.com/cOiWvcd"><img src="http://i.imgur.com/cOiWvcd.png" title="source: imgur.com" /></a>

Here we update our red view to have a different height and different
inset distance from it's parent view.

<a href="http://imgur.com/bd0Uqmg"><img src="http://i.imgur.com/bd0Uqmg.gif" title="source: imgur.com" /></a>

### Constraint Errors and Warnings

The Auto Layout system will give an error if it us unable to determine
the correct position and size of any of the views in your scene.  It
will provide you with a warning there is issue that may result
unexpected behavior&mdash;for example something that would result in
your interface not looking like it appears in interface builder.

#### Misplaced Views
As you edit your constraints you'll run into situations where the
position and/or size of your views as they appear in Interface Builder
no longer match what would be the result of the constraints you've
created.  In this case Auto Layout will will give you a "Misplaced
Views" warning.

##### Update frames
One way to fix this warning is to update the views' sizes and locations
in the Interface Builder to match the constraints.  You can do this by
selecting "Update frames" from the issues button or in the Auto Layout
error inspector.  You should use this option when you know that your
constraints are correct, and the way the views are laid out on the
canvas is wrong.  You should not select this option if you suspect one
of the constraints is wrong.  In particular, do not select this option
if Interface Builder says that you have "Missing Constraints" as this
will result in confusing placement of your view off screen or having it
be sized to zero.

Here we edit the red view's constraints for height, but this is not
properly updated on our canvas.  After selecting "Update Frames" the new
height is properly reflected.

<a href="http://imgur.com/U4mgd09"><img src="http://i.imgur.com/U4mgd09.gif" title="source: imgur.com" /></a>

##### Update constraints

Other times you'll be editing a view's location or size independently of
manipulating constraints, and the view's location on the canvas is the
location you want to keep.  You can update existing constraints to match
the view's location on the canvas by selecting "Update Constraints" from
the issues button or in the Auto Layout error inspector.

After "updating constraints", you should check to see if the system
modified your constraints in the a sensible way since sometimes
constraints will be updated in a way you did not intend.  In particular,
do not use this option if Interface Builder tells you that you have
"Conflicting Constraints" since this will update all constraints to fit
the location of the view on the canvas, and you will end up having
duplicate or redundant constraints.

Here we've decreased the width of the red view.  Selecting "Update
Constraints" changes the horizontal space constraints between the red
view and the parent view to match the new layout.

<a href="http://imgur.com/VCus7Pg"><img src="http://i.imgur.com/VCus7Pg.gif" title="source: imgur.com" /></a>

#### Conflicting Constraints
If you create constraints such that Auto Layout cannot simultaneously
satisfy all of your constraints (i.e. your system is overconstrained),
then it will give you an error about "Conflicting Constraints".  You'll
have to remove at least one constraint to resolve the issue.  Sometimes
it's helpful to remove all constraints for a particular view and start
over.  You can do this by selecting the view and choosing "Clear
Constraints" from the issues button.

Here our constraints on the red view to be centered along the y axis and
to be a specific distance from the top of the parent view conflict with
each other.  We resolve the issue by removing the vertical centering
constraint and updating the frame of the view.

<a href="http://imgur.com/x2ZKSyE"><img src="http://i.imgur.com/x2ZKSyE.gif" title="source: imgur.com" /></a>

#### Missing Constraints
If you do not provide enough information for Auto Layout to determine
both the x-coordinate and y-coordinate of the top left corner of your
view _and_ the width and height of your view, then it will give you an
error about "Missing Constraints".  This can be resolved by adding an
appropriate constraint.  Be careful when using the automatic issue
resolver since this may not add the constraint you expected or it may
add a relatively unintuitive constraint.

Here when we remove the constraint specifying the distance the red view
has to be away from the right side of the parent view, Auto Layout
can no longer determine the width of the red view.  Note that when we
use the automatic issue resolver it adds a width constraint instead of
the horizontal space constraint we originally had.

<a href="http://imgur.com/GjU98SU"><img src="http://i.imgur.com/GjU98SU.gif" title="source: imgur.com" /></a>

## Dealing with flexible content size
So far we have been only dealing with views whose content (and hence
size) does not change during run time.  However, many views that we end
up working with will have dynamic size depending on their content.  The
most prominent examples are [`UILabels`][uilabel],
[`UIButtons`][uibutton], and [`UIImageViews`][uiimageview].

[uilabel]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/
[uibutton]: https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIButton_Class/index.html
[uiimageview]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImageView_Class/index.html

### Intrinsic content size of a view
Views that can determine the size they that "should be" have what is
known as an _[intrinsic content size][intrinsiccontentsize]_.  This size
is the size the view has determined would be best to display its current
content.  For example a `UILabel`'s intrinsic content size will change
depending the text in the label, and a `UIImageView`'s intrinsic content
size will depend on the image it has loaded.

[intrinsiccontentsize]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ImplementingView/ImplementingView.html#//apple_ref/doc/uid/TP40010853-CH16-SW2

When using Auto Layout, you do not necessary have to provide width and
height constraints for views with an intrinsic content size since the
system will take this into account when computing the final layout of
the views.  To get a sense of how intrinsic content size works we can
consider some examples.

### Two labels: one on top of another
Here we add constraints for two labels that located vertically adjacent
to each other.  We pin the first label to the top left corner, specify
the vertical space between the labels, pin the second label to the left
margin and also give both labels a width.  Notice that we did not have
to specify the height for either label.

<a href="http://imgur.com/hhHeK7d"><img src="http://i.imgur.com/hhHeK7d.gif" title="source: imgur.com" /></a>

Now we make one the labels a multi-line label by setting it's "Lines"
property to 0 in the attributes inspector&mdash;this means that the
label can have an arbitray number of lines.  Once we add sufficiently
long text and update frames, the first label changes its height to match
the content and the second label gets automatically pushed lower down
the canvas so that the vertical space between the two labels is
maintained.  We had to update the frames manually here, but this would
be done automatically for us at runtime when we change the content of
the label.

<a href="http://imgur.com/FAhPo6v"><img src="http://i.imgur.com/FAhPo6v.gif" title="source: imgur.com" /></a>

### Left aligned label next to right aligned label
Consider another example where we have two single-line labels
horizontally adjancent to each other.  We want to pin one label to the
left margin and the other to the right margin&mdash;this is common for
example in the design of many table view cells.

#### Inequality constraints
We also want to specify that the labels should have a minimum amount of
horizontal space between them so that they do not run into each other.
We do not know the exact amount of horizontal space since the contents
of the labels might change at run time.  One way to accomplish this is
to define an _inequality constraint_ where we can specify that a certain
constraint's value be greater than or less than a constant.

<a href="http://imgur.com/by5X5AB"><img src="http://i.imgur.com/by5X5AB.gif" title="source: imgur.com" /></a>

#### Compression resistance
In this same example, what if the text in our labels becomes long enough
so overlap is unavoidable?  As seen below, at least one of the labels
will start to shrink and compress its content (in this case by using
an ellipsis).

How do we control this shrinking behavior?  Each view has a horizontal
and vertical _content compression resistance priority_ that can be
modified.  Higher compression resistance means the view is less likely
to shrink its content.

In this case we specify that the green label is the one whose content
should be compressed if there is a conflict by lowering its compression
resistance priority.

<a href="http://imgur.com/PhgyniJ"><img src="http://i.imgur.com/PhgyniJ.gif" title="source: imgur.com" /></a>

#### Content hugging
Sometimes you'll want to views to be a fixed distance from each other
and for one of the views to expand to fill the available
space&emdash;this is common for example with buttons.  This can be
accomplished by pinning the views to the surround views and adding a
fixed constraint for the space between them.  You can specify which view
should fill the available space by changing the _content hugging
priority_ of a view.  A lower content hugging priority means the view is
more likely to expand to match constraints, whereas a higher content
hugging priority means a view wants to be as close to its intrinsic
content size as possible.

Here we specify that the red label should expand to fill the available
space by lowering its content hugging priority.
<a href="http://imgur.com/B6txKC3"><img src="http://i.imgur.com/B6txKC3.gif" title="source: imgur.com" /></a>

## Manipulating constraints programmatically
## VFL

The Visual Format Language is a declarative language that is used to define Auto Layout constraints for views.

To use VFL, ensure `translatesAutoresizingMaskIntoConstraints` is set to `false`.

```Swift
  func addConstraints() {
    //Collect Views to apply VFL
    let buttonsDictionary = ["button1": flagButton1,
                         "button2": flagButton2,
                         "button3": flagButton3]

    //Metrics establish Fixed Constants
    let metrics = ["topSpacing": 80, "bottomSpacing": 20, "buttonHeight": 20, "buttonSpacing": 20]

    //Note that priorities can be set using @. 1000 for Required. < 100 for Optional. Example: @999

    //Horizontal constraints
    for buttonName in buttonsDictionary.keys {
      view.addConstraints(NSLayoutConstraint.constraintsWithVisualFormat("H:|-[\(buttonName)]-|", options: .allZeros, metrics: nil, views: buttonsDictionary))
    }

    //Vertical constraints
    view.addConstraints(NSLayoutConstraint.constraintsWithVisualFormat("V:|-(==topSpacing)-[button1(>=buttonHeight@997)]-(==buttonSpacing@999)-[button2(==button1)]-(==buttonSpacing@999)-[button3(==button1)]-(>=bottomSpacing@998)-|", options: .allZeros, metrics: metrics, views: buttonsDictionary))
  }
```

### Understanding debug output

## Other topics
### Springs and Struts and AutoResizing Mask

<!---
- autolayout is per interface builder document:
- http://stackoverflow.com/questions/9370072/xcode-4-3-not-presenting-autoresizing-panel-in-size-inspector
- Tip: If you want to remove all constraints and start over, choose Issues > Clear Constraints.
-->

<!---
- what is layout?
- why autolayout?
- specifies how to respond to change

- examples of most common use cases


- auto resizing mask / Springs and struts

- Tip: If you want to remove all constraints and start over, choose Issues > Clear Constraints.

- overconstrained
- underconstrained





-->