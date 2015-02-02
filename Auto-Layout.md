
- what is layout?
- why autolayout?
- specifies how to respond to change

- examples of most common use cases


- auto resizing mask / Springs and struts

- Tip: If you want to remove all constraints and start over, choose Issues > Clear Constraints.

- overconstrained
- underconstrained





## Overview

Traditional Cocoa layout is explicit. You set frame values, and how they should resize when the parent view resizes. If you needed something more complicated than simple "Springs and Struts," you would have to write the layout code yourself.

Auto Layout is about setting up rules for your views, such as "20 pixels between these two views," and "the left view is 200px wide," and "these views are vertically aligned." Auto Layout's engine figures how to resolve all *constraints*. This allows you to focus more on intention, instead of view code.

### Benefits

Auto Layout is useful when dealing with varying input. Say you're showing a list of user messages; traditionally, you'd have to calculate the height of the text, plus padding. With auto layout, the calculation is handled for you.

Auto Layout can simplify localization. For instance, it's a common pain to size buttons to fit text in languages like German, where words are significantly larger. With Auto Layout, the button can automatically size itself for its content.

Auto Layout is recommended when supporting multiple platforms. Say you want a view flush the tab bar in your universal app. Previously, you might have hardcoded the tab bar height at "49" on iPhone and "56" on iPad; alternately, you could have inspected the current platform's tab bar height at runtime, but that adds complexity. With Auto Layout, you just add a constraint between the view at the "bottom layout guide."

### Drawbacks

The biggest hurdle with Auto Layout is complexity. A rule based system can be overkill for really simple things; just ask anyone who has tried to center a DIV in CSS.

In a complex layout with many constraints, [performance should be considered](http://floriankugler.com/blog/2013/4/21/auto-layout-performance-on-ios). It probably a bad idea to use it with a tableview with a large number of cells.

## Constraints

A constraint is a rule. It could involve alignment, width, spacing, and more. Views cannot have ambiguous constraints, so expect four constraints per view. Constraints cannot contradict each other.

### Constant and Multiplier

Often constraints rely on the "constant" property. Say you want two views 20px apart; you will connect a constraint to the two views, and adjust the constant to "20".

There is also the multiplier property, which multiplies the constant. It defaults to 1. If you wanted to change the distance to 40px, you could either change the constant to "40", or set the multiplier to "2."

### Priorities

If multiple constraints compete with each other, the winner is determined by priority.

### Intrinsic Size

Certain views, like text labels, have a preferred size. Labels want to encompass the text within them. You could say the inner text defines its "Intrinsic Size." You can rely on this size to define the width of the view.

This is especially useful when localizing buttons with long text. You can specify where it should sit, and it will expand to fit its content.

### Compression Resistance and Content Hugging

Especially when dealing with text, views can have a certain elasticity. You may want a button to shrink as less space is available, until it is flush against the button text. Or you may want the button to expand when more space is available, adding more padding to the left and right of the text.

*Compression Resistance* is how much a view doesn't want to shrink. *Content Hugging* determines how much a view prefers to be flush with its content, e.g. a button preferring to be flush with its text.

### Layout Margins

Often, you'll space between a parent view and its subview. Rather than hard code these margins into the subview's constraints, specify them in the parent's layout margins. By default, subviews pinned to edges are pinned to the margins, rather than flush against the parent.

## Implementation

### Interface Builder

The most straightforward way to add constraints it through Interface Builder. In addition to providing a quick glance as to available constraints, and what constraints are installed on each view, it will provide warnings when constraints are ambiguous or contradictory.

### Programmatic

You can manually attach constraints in code using the NSLayoutConstraints. However, by default, auto resizing masks are translated into constraints so remember to turn off `translatesAutoresizingMaskIntoConstraints`.

Adding constraints one-by-one is extremely verbose, so the framework supports a "Visual Formatting Language." It is a domain specific language that resembles ASCII art. 

```
|-[findButton]-[findNextButton]-[findField(>=20)]-|
```

This specifies a "find" button, a "find next" button, and a text field that must be greater than or equal to 20.

To create these constraints, call `NSLayoutConstraint`'s `constraintsWithVisualFormat` method. It takes a string, and a dictionary that specifies the views those variables map to, e.g. "findButton" mapping to the UIButton instance.

## Resources

* [Achieving Zen with Auto Layout](http://vimeopro.com/360conferences/360idev-2013/video/75232527)
* [Apple's Auto Layout Guide](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html)
* [UIScrollView and Auto Layout](https://developer.apple.com/library/ios/technotes/tn2154/_index.html)

