## Overview
[View objects](Application-Architecture#views) encapsulate logic to
display information on the screen and respond to user events.  UIKit
comes with a [catalog of views][viewcatalog] that can be used to build
many kinds of user interfaces.  However, you can also define your own
[custom view][applecustomview] classes.  You might want to create a
custom view:
[viewcatalog]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/
[applecustomview]: https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/CreatingViews/CreatingViews.html#//apple_ref/doc/uid/TP40009503-CH5-SW23

1. to create a reusable component that appears in many places in your
   application(s).  The view can encapsulate both the visual appearance
and the behavior of the component.
2. to do customized drawing logic. You must create a subclass `UIView`
   in order to override [`drawRect`][drawrect].
3. to do low-level event handling since `UIView` is a subclass of
   [`UIResponder`][uiresponder].  This is rare.  Gesture recognizers are
generally preferred because they can be reused since they do not couple
the low-level event handling logic with the view.

[drawrect]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/instm/UIView/drawRect:
[uiresponder]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIResponder_Class/

## How views are defined and instantiated
In order to define our own custom views, we need to understand how views
are defined and created.  Each view is an instance of some subclass of
[`UIView`][uiview].  The class is responsible for defining the
_behavior_ of the view.  It may also be responsible for the layout and
_visual appareance_ of the view.

However, the layout and visual appearance of a view may be described in
a separate file created with Interface Builder (a `.xib` file).  We'll
refer to any Interface Builder file as a [_nib file_][nibfile]&mdash;the
naming here is for historical reasons.

_NB: Many of the things we describe for nibs will also apply to
storyboards, which are essentially nibs that can contain segues and can
only have view controllers at top-level objects.  In particular, the
process by which a storyboard instantiates its objects is mostly the
same._

[nibfile]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/LoadingResources/CocoaNibs/CocoaNibs.html
[uiview]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html

Views are generally created in one of two ways:

1. You can programatically instantiate a view by calling
   [`initWithFrame`][initwithframe].  This is generally done in a view
contoller's `viewDidLoad` method or in code that is responding to some
event.  In this case you will need to manually add the view to the view
hierarchy.

2. If a nib includes (possibly as a subview) a view of some (possibly
   custom) class, then when the nib is loaded, an instance of that class
will be created by calling [`initWithCoder`][initwithcoder].  If the
view was a subview then it will automatically be inserted into the
[top-level view][toplevelobjects]'s hierarchy.  If the view was a
top-level view, then you will have manually add the view to the view
hierarchy.

[initwithframe]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/instm/UIView/initWithFrame:
[initwithcoder]: https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Protocols/NSCoding_Protocol/index.html#//apple_ref/occ/intfm/NSCoding/initWithCoder:
[toplevelobjects]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/LoadingResources/CocoaNibs/CocoaNibs.html#//apple_ref/doc/uid/10000051i-CH4-SW17

If we want our custom views to support both use cases we'll have to
override both `initWithFrame` and `initWithCoder` in custom view
classes.

## Example: an image view with caption
To illustrate the different situations we'll come across when working
with custom views, we will implement a simple example.  Suppose our
application has many places where we need to display an image with a
caption.  We'll create a custom view `CaptionableImageView` that
contains a image view with a caption over a translucent gray background.

This is an example of encapsulating a reusable component with a custom
view.  For example, in a production application, you might add more
functionality to this class to allow customization of where the caption
is positioned or to make the caption disappear when the user taps on the
image.

<a href="http://imgur.com/isne6el"><img src="http://i.imgur.com/isne6ell.png" title="source: imgur.com" /></a>

## Using custom views defined programatically
We can define both the appearance and behavior of our custom view
programatically in the `CaptionableImageView` class as follows.

```swift
class CaptionableImageView: UIView {
    var label: UILabel!
    var imageView: UIImageView!

    var caption: String? {
        get { return label?.text }
        set { label.text = newValue }
    }

    var image: UIImage? {
        get { return imageView.image }
        set { imageView.image = newValue }
    }

    required init(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
        initSubviews()
    }

    override init(frame: CGRect) {
        super.init(frame: frame)
        initSubviews()
    }

    func initSubviews() {
        // sets the image's frame to fill our view
        imageView = UIImageView(frame: bounds)
        imageView.contentMode = UIViewContentMode.ScaleAspectFill
        imageView.clipsToBounds = true
        addSubview(imageView)

        // caption has translucent grey background 30 points high and span across bottom of view
        let captionBackgroundView = UIView(frame: CGRectMake(0, bounds.height - 30, bounds.width, 30))
        captionBackgroundView.backgroundColor = UIColor(white: 0.1, alpha: 0.8)
        addSubview(captionBackgroundView)

        label = UILabel(frame: captionBackgroundView.bounds.rectByInsetting(dx: 10, dy: 5))
        label.textColor = UIColor(white: 0.9, alpha: 1.0)
        captionBackgroundView.addSubview(label)
    }
}
```

Note that both our `initWithCoder` and `initWithFrame` methods call
another method `initSubviews` that does the real initialization work.
This is a common pattern when you need to create a custom view can be
used both programatically and within a nib.

### Programmatic instantiation
To use this programatically is fairly straightforward.  We simply
instantiate the view and add it as subview.

```swift
class ViewController: UIViewController {

    var imageView: CaptionableImageView!

    override func viewDidLoad() {
        super.viewDidLoad()

        // we'd probably want to set up constraints here in a real app
        imageView = CaptionableImageView(frame: CGRectMake(0, 20, view.bounds.width, 200))
        imageView.image = UIImage(named: "yodawg")
        imageView.caption = "Yo dawg, I heard you like views"
        view.addSubview(imageView)
    }
}
```

### Embedding in a nib or storyboard
To use our custom view inside a nib we simply drag in a `View` (colored
orange below for visibility) from the Object Library and set the view's
custom class in the attributes inspector.

<a href="http://imgur.com/BnVinUM"><img src="http://i.imgur.com/BnVinUM.png" title="source: imgur.com" /></a>

Here we've added the custom view to our main view controller in the
storyboard.  We can get a reference to this view as we would any other
by creating an `@IBOutlet`.  You can verify that the `initWithCoder`
method is called for `CaptionableImageView` when storyboard is loaded by
adding a breakpoint.

```swift
import UIKit

class ViewController: UIViewController {

    @IBOutlet weak var imageView: CaptionableImageView!

    override func viewDidLoad() {
        super.viewDidLoad()
        imageView.image = UIImage(named: "yodawg")
        imageView.caption = "Yo dawg, I heard you like views"
    }
}
```

## Nibs and how they are loaded
In order to work with custom views whose layout is defined in a nib, we
need to learn more about what exactly a nib is and how it is are loaded.

Nibs declaratively define the layout and configuration of objects in
your application&mdash;most of the time you'll use them to configure
views and view controllers, but arbitrary objects can be configured in
Interface Builder by dragging in an `Object ` item from the Object
Library.

Nibs also contain information about how these objects are related to
each other.  In particular, a nib can set a property on an object to
point to another object via [outlets][outlets].

[outlets]: https://developer.apple.com/library/ios/documentation/General/Conceptual/CocoaEncyclopedia/Outlets/Outlets.html

### The file's owner object
Of particular importance is the ability to have outlets to and from
objects that are not defined inside the nib.  This means that you can
have a nib define references to and from an object that does not have to
be provided until the nib is loaded.  For example a view object might
have a delegate property that needs to be bound to an object that is
created by your application at runtime.  Or alternatively, your custom
class might need a reference to a label defined inside the nib.

Interface Builder allows you to accomplish these things by providing a
placeholder [file's owner][filesowner] object.  You can define outlets
from the file's owner to objects in your nib by setting its custom class
and then using the assistant editor and control dragging
objects into the custom class.

For example in our `CaptionableImageView` [example](#example-an-image-view-with-caption)
we might define our label and image view inside the nib and add outlets
for them in the file's owner:

<a href="http://imgur.com/tgBhRiF"><img src="http://i.imgur.com/tgBhRiF.gif" title="source: imgur.com" /></a>

Note that **changing the file's owner's custom class only defines this
relationship temporarily to help you and Interface Builder create the
outlets**.  It does not define a runtime relationship between your class
and nib.  In particular, creating an instance of your custom class will
not load elements from the nib for you, and vice-versa loading the nib
will not create an instance of the custom class for you.

[filesowner]: https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/LoadingResources/CocoaNibs/CocoaNibs.html#//apple_ref/doc/uid/10000051i-CH4-SW15

### Nib loading process
A nib can be manually loaded by creating an instance of [`UINib`][uinib] and
then calling [`instantiateWithOwner`][instantiatewithowner].

[uinib]: https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UINib_Ref/index.html
[instantiatewithowner]: https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UINib_Ref/index.html#//apple_ref/occ/instm/UINib/instantiateWithOwner:options:

```swift
        // nil here means use the default main bundle
        let nib = UINib(nibName: "YourNibName", bundle: nil)

        // objects is an array of all top-level objects in the nib
        let objects = nib.instantiateWithOwner(yourOwnerObject, options: nil)
```

Two important things happen when `instantiateWithOwner` is called:

1.  Every object described in the nib is initialized.  In particular,
    any view objects will have an instance of their (possibly custom)
class created via `initWithCoder`.
2.  Any property connected by an outlet is set to the appropriate
    object.  In particular, any outlets on file's owner will be set _on the owner object you passed in_.

For example, if our nib was defined to have `imageView` and `label`
outlets on the file's owner object as [above](#the-files-owner-object), we
might load it as follows

```swift
        let captionableImageView = CaptionableImageView(frame: CGRectMake(0, 20, view.bounds.width, 200))
        let nib = UINib(nibName: "CaptionableImageView", bundle: nil)
        let objects = nib.instantiateWithOwner(captionableImageView, options: nil)

        // in this case the only top-level object is the top level view
        captionableImageView.addSubview(objects.first as UIView)

        captionableImageView.imageView.image = UIImage(named: "yodawg")
        captionableImageView.label.text = "Yo dawg, I heard you like views"
```

A few things to note here:

* We need to instantiate a CaptionableImageView separately to serve as
  our file's owner.  This means that that the `imageView` and `label`
properties on `captionableImageView` will be set to the corresponding
image view and label described in the nib.

* We could have used any other object with
[key-value coding compliant][kvccompliant]
properties `imageView` and `label` as the owner and its properties would
be set to the corresponding objects in the nib.  If we pass in an object
that does not contain one of these properties we will have a runtime
error.  This is the source of the common error

```
... 'NSUnknownKeyException', reason: '[<...> setValue:forUndefinedKey:]: this class is not key value coding-compliant for the key ...

```
* The objects returned to us are not part of any view hierarchy.  Here
  we have to add the top-level `UIView` to as a subview of our
`CaptionableImageView`.

<!---
* If a view will only be used once in one view controller, it is common
  to have the file's owner be the view controller and to bind any
necessary elements in the nib directly to properties in the view
controller.  For example if a nib defines a view controller's root view


[initwithnibnamed]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/initWithNibName:bundle:
-->

[kvccompliant]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/KeyValueCoding/Articles/KeyValueCoding.html

## Using custom views defined in a nib
In this section we'll reimplement our
[example](#example-an-image-view-with-caption) custom view to load its
layout from a nib.

First we create a subclass of `CaptionableImageView` of `UIView` as before.
We'll then need to create our nib by selecting `File -> New -> File...
-> iOS -> User Interface -> View`.  It is customary to give this file
the same name as your class, so we'll also name it
`CaptionableImageView` (the `.xib extension gets added automatically).

We can now open the nib and add the image view, label background, and label
to our top-level view in interface builder.  In this case we'll also add
auto layout constraints so that our image expands with with the
top-level view and the label and label background are pinned to the
bottom of the top-level view.

As [above](#the-files-owner-object), we set the file's owner's custom
class to `CaptionableImageView` and create outlets for the image view
and label.  We'll create an additional outlet to the top level view
called `contentView`.  The reason for this will be apparent soon.

<a href="http://imgur.com/JsnxD5D"><img src="http://i.imgur.com/JsnxD5D.gif" title="source: imgur.com" /></a>

Finally we add the code for our `CaptionableImageView` class as follows

```swift 
class CaptionableImageView: UIView {

    @IBOutlet var contentView: UIView!
    @IBOutlet weak var label: UILabel!
    @IBOutlet weak var imageView: UIImageView!

    var caption: String? {
        get { return label?.text }
        set { label.text = newValue }
    }

    var image: UIImage? {
        get { return imageView.image }
        set { imageView.image = newValue }
    }

    required init(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
        initSubviews()
    }

    override init(frame: CGRect) {
        super.init(frame: frame)
        initSubviews()
    }

    func initSubviews() {
        let nib = UINib(nibName: "CaptionableImageView", bundle: nil)
        nib.instantiateWithOwner(self, options: nil)
        contentView.frame = bounds
        imageView.contentMode = UIViewContentMode.ScaleAspectFill
        imageView.clipsToBounds = true
        addSubview(contentView)
    }
}
```

Regardless of being instantiated either from `initWithCoder` or `initWithFrame`,
we load the nib with this `CaptionableImageView` as the owner.  This
will set the `label` and `imageView` properties to point to the ones
described in the nib.

It will also set `contentView` to the only top level-view in our nib.
Note however that the top-level objects returned from
`instantiateWithOwner` are not added to any view hierarchy.  Therefore
we add `contentView` as a subview of our `CaptionableImageView` and tell
it to take up all the space available to us.

_NB: In this case, `contentView` is actually the same as the first
object in the array returned by `instantiateWithOwner`. However creating
an outlet for it is slightly safer and allows us to define more than one
top-level view in the nib._

### Rendering custom view in Interface Builder
Custom views added inside another nib or storyboard will not render in the InterFace Builder canvas by default. If we want our custom views to appear like any other UIKit view while we design in Interface Builder we just need to set the custom view class to be `@IBDesignable` and then initialize or nib with bundle, `Bundle(for: type(of: self))`

Modify the above snippet like this to get your custom view rendering in Interface Builder canvass:

```swift
// Tell Interface Builder to render in storyboard canvas
@IBDesignable
class CaptionableImageView: UIView {
...
    func initSubviews() {
        // Set bundle
        let nib = UINib(nibName: "CaptionableImageView", bundle: Bundle(for: type(of: self)))   
    ...
    }
...
}
```

### Loading programatically
How we use the `CaptionableImageView` actually remains exactly the same
[as before](#programmatic-instantiation) when we defined it completely
programatically.  We call `initWithFrame` and the logic inside the
`CaptionableImageView` itself handles the loading of the nib and
management of its internal view hierarchy for us.

```swift
class ViewController: UIViewController {
    var imageView: CaptionableImageView!

    override func viewDidLoad() {
        super.viewDidLoad()

        // we'd probably want to set up constraints here in a real app
        imageView = CaptionableImageView(frame: CGRectMake(0, 20, view.bounds.width, 200))
        imageView.image = UIImage(named: "yodawg")
        imageView.caption = "Yo dawg, I heard you like views"
        view.addSubview(imageView)
    }
}
```

### Within a another nib or storyboard
Again the procedure to use the `CaptionableImageView` inside another nib
or storyboard remains exactly the same [as
before](#embedding-in-a-nib-or-storyboard) when we defined the custom
view programatically.  We add a `View` object to our nib/storyboard in
Interface Builder and set its custom class to `CaptionableImageView`.
When this nib/storyboard is loaded it will call `initWithCoder` which
will load `CaptionableImageView.xib` and set up the internal view
hierarchy and outlets for us.

### Why you shouldn't set top-level view's custom class

Recall from [above](#how-views-are-defined-and-instantiated) that when
`instantiateWithOwner` is called on a nib, any view in the nib will be
instantiated by calling [`initWithCoder`][initwithcoder] on that view's
(possibly custom) class.  You might be wondering then why we don't
simply set the custom class of the top-level view to be our custom class
`CaptionableImageView`.  This would save us the trouble of having to
mess about with "file's owner".

In fact we can do this if we manually load the nib everywhere we plan to
use `CaptionableImageView`.  For example suppose we removed all the outlets
from file's owner, set the top-level view's custom class to
`CaptionableImageView` and recreated the outlets for `imageView` and
`label`:

<a href="http://imgur.com/NAKk6UU"><img src="http://i.imgur.com/NAKk6UU.png" title="source: imgur.com" /></a>

The code for `CaptionableImageView` looks like:

```swift
class CaptionableImageView: UIView {

    @IBOutlet var label: UILabel!
    @IBOutlet var imageView: UIImageView!

    var caption: String? {
        get { return label?.text }
        set { label.text = newValue }
    }

    var image: UIImage? {
        get { return imageView.image }
        set { imageView.image = newValue }
    }
}
```

We can then load this view in view controller by instantiating the nib
and extract the first object.  We can pass in `nil` as file's owner
meaning don't set any outlets on file's owner.

```swift
class ViewController: UIViewController {

    var imageView: CaptionableImageView!

    override func viewDidLoad() {
        super.viewDidLoad()

        let nib = UINib(nibName: "CaptionableImageView", bundle: nil)
        let objects = nib.instantiateWithOwner(nil, options: nil)
        imageView = objects.first as CaptionableImageView
        imageView.image = UIImage(named: "yodawg")
        imageView.caption = "Yo dawg, I heard you like views"
        view.addSubview(imageView)
    }
}
```

This works perfectly fine except for the fact that we now have to
load the nib `CaptionableImageView.xib` everywhere we want to
use `CaptionableImageView`.  In particular it is not possible to embed
`CaptionableImageView` as a subview in another nib/storyboard. This is
because when the `initWithCoder` method is called on
`CaptionableImageView` during the outer nib's loading process, nothing
happens since we are not loading the `CaptionableImageView.xib`
anywhere.

What happens now if we try to add back the logic for loading the nib inside
`initWithCoder`/`initWithFrame`?

```swift
class CaptionableImageView: UIView {
    ...

    required init(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
        initSubviews()
    }

    override init(frame: CGRect) {
        super.init(frame: frame)
        initSubviews()
    }

    func initSubviews() {
        let nib = UINib(nibName: "CaptionableImageView", bundle: nil)
        nib.instantiateWithOwner(self, options: nil)
    }
}
```

Now suppose we add a view to our storyboard with custom class
`CaptionableImageView`.  When our storyboard is loaded it will call our
`initWithCoder` method.  This will then try to load the nib in
`initSubviews`.  However, since the top-level view in the nib had its
custom class set to `CaptionableImageView`, the nib loading process will
then call our `initWithCoder` method again!  **We are stuck in an
infinite loop if loading the nib triggers loading the same nib again in
`initWithCoder`.**

A common pattern to get around this is to set our custom class and bind
all the outlets to file's owner as we did
[above](#using-custom-views-defined-in-a-nib).  Then loading the nib in
`initWithFrame`/`initWithCoder` will not trigger another `initWithCoder`
on our custom class when the top-level view is instantiated.

## A note about view controllers
So far we have only discussed using nibs with views.  However, something that is relatively common is to instantiate a nib with the
file's owner set to a view controller.  This allows you bind outlets to
elements inside a nib directly to properties inside your view
controller.

Perhaps the most common usage of this is in the view controller's
[`initWithNibName`][initWithNibName]
method.  This will load the nib, instantiate the view controller, and
set the view controller's view to be the top-level view instantiated
from the nib.  It will also bind any outlets on the file's owner object
to properties in view controller.

[initwithnibname]: https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/ViewLoadingandUnloading/ViewLoadingandUnloading.html#//apple_ref/doc/uid/TP40007457-CH10

The process by which a view controller gets loaded from a storyboard is
similar except that it will call the view controllers `initWithCoder`
method and set up the nib separately.  More information on that process
can be found [here][storyboardloading]

[storyboardloading]: https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/ViewLoadingandUnloading/ViewLoadingandUnloading.html#//apple_ref/doc/uid/TP40007457-CH10