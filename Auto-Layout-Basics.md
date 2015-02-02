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
if Interface Builder tells you have "Missing Constraints" as this will
result in confusing placement of your view off screen or having it be
sized to zero.

Here we edit the red view's constraints for height, but this is not
properly updated on our canvas.  After selecting "Update Frames" the new
height is properly reflected.

<a href="http://imgur.com/U4mgd09"><img src="http://i.imgur.com/U4mgd09.gif" title="source: imgur.com" /></a>

##### Update constraints
Other times the

Other times you'll be editing a view's location or size independently of
manipulating constraints.
   - can change your constraints in unexpected ways
   - don't update constraints when overconstrained since this may end up
  creating duplicate constraints

<a href="http://imgur.com/VCus7Pg"><img src="http://i.imgur.com/VCus7Pg.gif" title="source: imgur.com" /></a>

#### Conflicting Constraints

<a href="http://imgur.com/x2ZKSyE"><img src="http://i.imgur.com/x2ZKSyE.gif" title="source: imgur.com" /></a>

#### Missing Constraints
<a href="http://imgur.com/GjU98SU"><img src="http://i.imgur.com/GjU98SU.gif" title="source: imgur.com" /></a>

- be careful when adding constraints as it may not add the one you want

## Dealing with flexible content size

### Intrinsic content size of a view

### Two labels: one on top of another

### Left aligned label next to right aligned label
#### Compression resistance

### Left aligned multiline label next to right aligned label
#### Content hugging

## Manipulating constraints programmatically
## VFL

### Understanding debug output

## Other topics
### Springs and Struts and AutoResizing Mask

<!---
- autolayout is per interface builder document:
- http://stackoverflow.com/questions/9370072/xcode-4-3-not-presenting-autoresizing-panel-in-size-inspector
- Tip: If you want to remove all constraints and start over, choose Issues > Clear Constraints.
-->
