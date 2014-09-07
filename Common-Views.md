## Overview

These are the most common views used on iOS.

### UILabel

Labels display text. It defaults to a single line, but if you set the line count to 0, it will use multiple lines.

* [UIKit Catalog: Labels](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UILabel.html)
* [UILabel Class Reference](https://developer.apple.com/Library/ios/documentation/UIKit/Reference/UILabel_Class/Reference/UILabel.html)

### UIImageView

Image Vies display images. By default, user-interaction is disabled. If you attach gestures to the view, and nothing happens, remember to re-enable it.

* [UIKit Catalog: Image Views](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UIImageView.html)
* [UIImageView Class Reference](https://developer.apple.com/library/ios/documentation/uikit/reference/uiimageview_class/Reference/Reference.html)

### UIButton

You can assign buttons to trigger code on tap, to "touch up inside" event. You could also trigger code on other events, like when the user first presses down, but hasn't lifted their finger.

* [UIKit Catalog: Buttons](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UIButton.html)
* [UIButton Class Reference](https://developer.apple.com/library/ios/documentation/uikit/reference/UIButton_Class/UIButton/UIButton.html)

### UITextField

Text fields are for single lines of text input, like a form field. They're similar to an HTML `input` tag of type `text`.

* [UIKit Catalog: Text Fields](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UITextField.html)
* [UITextField Class Reference](https://developer.apple.com/library/ios/documentation/uikit/reference/UITextField_Class/Reference/UITextField.html)

### UITextView

UITextView is for long blocks of (possibly editable) text.

* [UIKit Catalog: Text Views](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UITextView.html)
* [UITextView Class Reference](https://developer.apple.com/library/ios/documentation/uikit/reference/uitextview_class/Reference/UITextView.html)

### UIScrollView

Unlike a webpage, if your view is too large to fit on screen, it won't automatically become scrollable. If you want to make a large view scrollable, place it inside a scrollview.

* [UIKit Catalog: Scroll Views](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UIScrollView.html)
* [Scroll View Programming Guide](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/UIScrollView_pg/Introduction/Introduction.html)
* [UIScrollView Class Reference](https://developer.apple.com/library/ios/documentation/uikit/reference/uiscrollview_class/Reference/UIScrollView.html)

### UITableview

Table views display long lists of items. It one of the most common root views on iOS. These are covered in greater detail in [[Table View Basics|Tableview Basics]].

* [UIKit Catalog: Table Views](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UITableView.html)
* [Table View Programming Guide](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/TableView_iPhone/AboutTableViewsiPhone/AboutTableViewsiPhone.html)
* [UITableView Class Reference](https://developer.apple.com/library/ios/documentation/uikit/reference/UITableView_Class/Reference/Reference.html)