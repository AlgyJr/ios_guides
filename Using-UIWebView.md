Loading a URL into your app is as easy! Just add and configure a UIWebView.  
  
![WebView Demo|250](http://i.imgur.com/v4PlBQX.gif)

### Step 1: Add UIWebView to Storyboard

Drag and drop a UIWebView from the Object Library to the main view of your ViewController. Drag the edges of the view to adjust it's size. 

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
let requestURL = NSURL(string:url)
// Place the URL in a URL Request.
let request = NSURLRequest(URL: requestURL!)
// Load Request into WebView.
webView.loadRequest(request)
```

### Step 6: Add Networking Permissions

- [App Transport Security Guide](http://guides.codepath.com/ios/App-Transport-Security)

