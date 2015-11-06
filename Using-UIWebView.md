Loading a URL into your app is as easy! Just add and configure a UIWebView.

### Step 1: Add UIWebView to Storyboard

- Drag and drop a UIWebView from the Object Library to the main view of your ViewController.


### Step 2: Create a custom ViewController file

- Choose: File -> New -> File...-> iOS -> Cocoa Touch Class

### Step 3: Create an Outlet for the UIWebView

- Create an outlet for the WebView by ctrl + dragging from the WebView in the Storyboard to your custom ViewController swift file.

### Step 4: Create Constant for URL

- Above the `viewDidLoad` create a constant to hold your URL string. We will link to the DropBox mobile terms url.

```swift
let url = "https://www.dropbox.com/terms?mobile=1"
``` 

Create a String which contains your URL
Convert the String to a NSURL object.
Place the URL in a URL Request.
Load Request

