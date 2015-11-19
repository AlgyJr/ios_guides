## Overview
The iOS framework comes with many [views][viewcatalog] that cover common
user interface elements.  You can also create a _custom view_ to to
encapsulate the visual appearance and behavior of your own reusable
component.

[viewcatalog]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/

For example, in a news reading application we might want a standard
component to display images with a caption using a consistent style on
both the home page view controller as well as the individual story view
controller.  This quickstart gives a recipe for making a custom view in
Interface Builder and using it in another part of your application.

## Creating a Custom View
### Step 1: Design custom view Interface Builder
Create a `.xib` file by selecting `File -> New -> File... -> iOS -> User
Interface -> View`.  Name your file something something descriptive like
`CaptionableImageView`.  Open this file Interface Builder and start
designing your view by dragging in components from the Object Library.

### Step 2: Create subclass of UIView
Create a subclass of `UIView` by selecting `File -> New -> File... ->
iOS -> Source -> Cocoa Touch Class`.  Normally you'll want to name your
class to match your `.xib` file.

### Step 3: Set custom class in Interface Builder
Open your `.xib` file and select the `File's Owner` object in the
Document Outline.  Select the custom class of `File's Owner` to the
class you just created.

<a href="http://imgur.com/HyiX5S7"><img src="http://i.imgur.com/HyiX5S7.png" title="source: imgur.com" /></a>

### Step 4: Connect outlets
Open the Assistant Editor and pick your custom class.  Create outlets
for anything you'll need to manipulate or set programatically.

<a href="http://imgur.com/tgBhRiF"><img src="http://i.imgur.com/tgBhRiF.gif" title="source: imgur.com" /></a>

### Step 5: Create outlet for content view
Create an outlet for the top-level view in your `.xib` and call it
`contentView`.

<a href="http://imgur.com/JsnxD5D"><img src="http://i.imgur.com/JsnxD5D.gif" title="source: imgur.com" /></a>

### Step 6: Add initialization code
In the custom view class, you'll need to override the `initWithCoder` and
`initWithFrame` methods to load the `.xib` and add the content
view to the view hierarchy.

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
        // standard initialization logic
        let nib = UINib(nibName: "CaptionableImageView", bundle: nil)
        nib.instantiateWithOwner(self, options: nil)
        contentView.frame = bounds
        addSubview(contentView)

        // custom initialization logic
        ...
    }
}
```

### Step 7: Add code that allows configuration of your custom view
Finish adding the rest of the code for your custom class.  You'll want
to give users of this class a simple way to configure the elements of
the view without exposing them to uncessary details.  For example, in our
`CaptionableImageView` we can provide more convenient ways to set the
image and caption.

```swift
class CaptionableImageView: UIView {

    @IBOutlet private var contentView: UIView!
    @IBOutlet private weak var label: UILabel!
    @IBOutlet private weak var imageView: UIImageView!

    var caption: String? {
        get { return label?.text }
        set { label.text = newValue }
    }

    var image: UIImage? {
        get { return imageView.image }
        set { imageView.image = newValue }
    }
    ...
}
```

## Using a Custom View
Once you've finish your custom view you can now use it pretty much as
you would any other view.

### Using a custom view in storyboards
Open up your story board and drag a `View` (colored orange below for
visibility) from the Object Library into your view controller.  Set the
view's custom class to your custom view's class.

<a href="http://imgur.com/BnVinUM"><img src="http://i.imgur.com/BnVinUM.png" title="source: imgur.com" /></a>

Create an outlet for the custom view in your view controller.  A good
place to configure the view is in `viewDidLoad`.

```swift
import UIKit

class ViewController: UIViewController {

    @IBOutlet weak var imageView: CaptionableImageView!

    override func viewDidLoad() {
        super.viewDidLoad()
        imageView.image = UIImage(named: "codepath_logo")
        imageView.caption = "CodePath starts new class for designers"
    }
}
```

### Using a custom view programatically
To instantiate your view programatically, you can call `initWithFrame`
on your custom class.  You'll have to manually add it as subview.

```swift
class ViewController: UIViewController {

    var imageView: CaptionableImageView!

    override func viewDidLoad() {
        super.viewDidLoad()

        // we'd probably want to set up constraints here in a real app
        imageView = CaptionableImageView(frame: CGRectMake(0, 20, view.bounds.width, 200))
        imageView.image = UIImage(named: "codepath_logo")
        imageView.caption = "CodePath starts new class for designers"
        view.addSubview(imageView)
    }
}
```

## Further reading
* [Our custom views guide](Custom-Views#) for a more in depth discussion
* [Apple's official view programming guide][appleguide]

[appleguide]: https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/CreatingViews/CreatingViews.html#//apple_ref/doc/uid/TP40009503-CH5-SW23

