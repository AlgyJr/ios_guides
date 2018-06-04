Loading a URL into your app is as easy! Just add and configure a WebKit View, which replaces UIWebView starting iOS 8.  
  
![WebView Demo|250](https://i.imgur.com/v4PlBQX.gif)

### Step 0: Add WebKit to your project
Add WebKit to your project's "Linked Frameworks and Libraries"

### Step 1: Add WebKit View (not UIWebView) to Storyboard

Drag and drop a WebKit View from the Object Library to the main view of your ViewController. Drag the edges of the view to adjust it's size. 

### Step 2: Create a custom ViewController file

Choose: File -> New -> File...-> iOS -> Cocoa Touch Class

### Step 3: Create an Outlet for the UIWebView

Create an outlet for the WebView by ctrl + dragging from the WebView in the Storyboard to your custom ViewController swift file.

### Step 4: Create Constant for URL

**Above** the `viewDidLoad` method, create a constant to hold your URL string. We will link to the DropBox mobile terms url.

```swift
let url = "https://www.dropbox.com/terms?mobile=1"
``` 

### Step 5: Convert String and Load Request

Within the `viewDidLoad` method... 

```swift
// Convert the url String to a NSURL object.
let requestURL = URL(string:url)
// Place the URL in a URL Request.
let request = URLRequest(URL: requestURL!)
// Load Request into WebView.
webView.loadRequest(request)
```

### Step 6: Add Networking Permissions

- [App Transport Security Guide](http://guides.codepath.com/ios/App-Transport-Security)

