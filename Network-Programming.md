<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Overview of popular network programming methods](#overview-of-popular-network-programming-methods)
- [A note about threading and network requests](#a-note-about-threading-and-network-requests)
- [Example: Making a GET and parsing a JSON response](#example-making-a-get-and-parsing-a-json-response)
  - [NSURLConnection](#nsurlconnection)
  - [NSURLSession](#nsurlsession)
  - [AFNetworking](#afnetworking)
- [Example: loading images asynchronously using UIImageView+AFNetworking](#example-loading-images-asynchronously-using-uiimageviewafnetworking)
- [Example: OAuth 1](#example-oauth-1)
- [Example: OAuth 2](#example-oauth-2)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Many iOS application are clients of a REST API.  This guide gives an
overview of common ways to accomplish tasks associated with making
*HTTP* requests and handling responses.  Low level socket programming,
although possible to do in iOS is out of the scope of this guide.

## Overview of popular network programming methods
There is a wide variety of ways to make HTTP requests in iOS with which
you might at least want to be familiar.

**[NSURLConnection][nsurlconnectionguide]** is a lower level mechanism
to make URL requests and is part of Apple's Foundation Framework.

* simplest way to make URL request
* provides synchronous and asynchronous requests via with completion
  handler blocks and delegates
* does not have much support for authentication or a session concept
* does not have an "operation" or "task" concept associated with
  requests so there's no convenient way to handle queue of requests or
  to pause/resume
* does not handle parsing of common content types
* not much built in support for HTTP error codes / request parameters

[nsurlconnectionguide]: https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/URLLoadingSystem/Tasks/UsingNSURLConnection.html

**[NSURLSession][nsurlsessionguide]** a higher level library that is part
of Apple's Foundation Framework.

* built on top of NSURLConnection
* better support for authentication and has a session concept
* concept of "task" enables pausing/resuming requests
* can perform requests while your app is in the background
* does not handle parsing of common content types
* not much built in support for HTTP error codes / request parameters

[nsurlsessionguide]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/URLLoadingSystem/Articles/UsingNSURLSession.html

**[AFNetworking][afnetworking]:** is the most popular library for and is
the de facto gold standard for networking tasks in the iOS world.
**Chances are you will want to use this library if accessing an API
and making network requests is a key part of your application.**

* built on top of NSURLSession
* built-in support for parsing common content-types
* great support for common HTTP operations including handling of request
  params, headers, error codes
* great integration with UIKit components making complex behavior like
  loading remote images asynchronously very easy

[afnetworking]: https://github.com/AFNetworking/AFNetworking/

**[AlamoFire][alamofire]:** is another networking library by the same
author as AFNetworking.  It is written in Swift.

* Swift only
* many of the same features as AFNetworking
* easy to use/read syntax for making common requests
* no integration with UIKit

[alamofire]: https://github.com/Alamofire/Alamofire

## A note about threading and network requests
In iOS much of the code that runs in your application is triggered by an
event on the [main event loop][maineventloop].  The main event loop is
responsible for executing code to respond to things like user
interaction (e.g triggering an `@IBAction`) or events in a view
controller's lifecycle (e.g.  `viewDidLoad`).  Code executed from the
main event loop is run on the _main thread_.  This is convenient for us
because __any updates to an application's UI elements must happen on the
main thread__.  We'll want to keep this rule in mind when working with
network requests.

[maineventloop]: https://developer.apple.com/library/ios/documentation/General/Conceptual/Devpedia-CocoaApp/MainEventLoop.html

iOS provides a couple of higher level libraries for concurrent
programming: [Grand Central Dispatch][gcd] and
[NSOperationQueue][operationqueues].  You'll be able use either to
ensure you that a piece of code does or does not run on the main thread.

[gcd]: https://developer.apple.com/library/mac/documentation/Performance/Reference/GCD_libdispatch_Ref/index.html
[operationqueues]: https://developer.apple.com/library/mac/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationObjects/OperationObjects.html

You should never make a synchronous network request on the main thread
since this will block thread and UI will appear frozen while our request
is pending.  You'll rarely run into instances where you'll need
to make synchronous requests

When we make an asynchronous request, any of the above libraries will
execute the request on a background (i.e. not the main) thread.  Some
methods will allow us to specify the dispatch queue on which we want the
response handler to run, others will provide no guarantees.  **If we
need to update the UI in our response handler we must ensure that the
code that manipulates the UI is run on the main thread.**  This can be
tricky because we may call into a method that calls into another method
that after a long stack of calls eventually updates a UI element.

One simple way to ensure a block of cose is run on the main thread using
Grand Central Dispatch is as follows

```swift
dispatch_async(dispatch_get_main_queue(), {
  // this code will be executed on the main thread
})
```

## Example: Making a GET and parsing a JSON response
<!-- Add links to nyt example -->
In order to provide you with the flavor of each of the major ways of
making network requests we discussed above, we'll rework the network
code of our NY Times Viewer application from the table view guide to
use each method.

### NSURLConnection
Notice that we are forced to specify a operation queue on which the
completion handler will run.

```swift
import UIKit

private let apiKey = "53eb9541b4374660d6f3c0001d6249ca:19:70900879"
private let resourceUrl = NSURL(string: "http://api.nytimes.com/svc/topstories/v1/home.json?api-key=\(apiKey)")!

class Story {
    var headline: String?
    var thumbnailUrl: String?

    init(jsonResult: NSDictionary) {
        ...
    }

    class func fetchStories(successCallback: ([Story]) -> Void, error: ((NSError?) -> Void)?) {
        let request = NSURLRequest(URL: resourceUrl)
        NSURLConnection.sendAsynchronousRequest(request, queue: NSOperationQueue.mainQueue()) { (response, data, requestError) -> Void in
            if let requestError = requestError? {
                error?(requestError)
            } else {
                if let data = data? {
                    let json = NSJSONSerialization.JSONObjectWithData(data, options: nil, error: nil) as NSDictionary
                    if let results = json["results"] as? NSArray {
                        var stories: [Story] = []
                        for result in results as [NSDictionary] {
                            stories.append(Story(jsonResult: result))
                        }
                        successCallback(stories)
                    }
                } else {
                    // unexepected error happened
                    error?(nil)
                }
            }
        }
    }
}
```

### NSURLSession
Since we don't need to authenticate or download something while our app
is in the background, the `NSURLSession` code for this simple request is
pretty similar to our `NSURLConnection` code.  Notice that there are no
guarantees on which thread the completion handler will run.  The caller of
our `fetchStories` method passes in a `successCallback`, but we also
make no guarantees on which thread the callback will run and leave it up
to the caller to ensure anything he does in the callback will be run on
the correct thread.


```swift
private let apiKey = "53eb9541b4374660d6f3c0001d6249ca:19:70900879"
private let resourceUrl = NSURL(string: "http://api.nytimes.com/svc/topstories/v1/home.json?api-key=\(apiKey)")!

class Story {
    var headline: String?
    var thumbnailUrl: String?

    init(jsonResult: NSDictionary) {
      ...
    }

    class func fetchStories(successCallback: ([Story]) -> Void, error: ((NSError?) -> Void)?) {
        NSURLSession.sharedSession().dataTaskWithURL(resourceUrl, completionHandler: {(data, response, requestError) -> Void in
            if let requestError = requestError? {
                error?(requestError)
            } else {
                if let data = data? {
                    let json = NSJSONSerialization.JSONObjectWithData(data, options: nil, error: nil) as NSDictionary
                    if let results = json["results"] as? NSArray {
                        var stories: [Story] = []
                        for result in results as [NSDictionary] {
                            stories.append(Story(jsonResult: result))
                        }
                        successCallback(stories)
                    }
                } else {
                    // unexepected error happened
                    error?(nil)
                }
            }
        }).resume()
    }
}
```

### AFNetworking
This code starts to look a little cleaner with AFNetworking.
AFNetworking does some error handling for us and gives us a way to
provide a failure handler.  Also note that we no longer have to parse
the JSON result ourselves since AFNetworking does this automatically
based on the content type.  Finally note that we were able to supply our
GET parameters as a Swift dictionary.  This is not particularly useful
here, but becomes very nice to have if there is a large number of
paramters.

```swift
private let params = ["api-key": "53eb9541b4374660d6f3c0001d6249ca:19:70900879"]
private let resourceUrl = "http://api.nytimes.com/svc/topstories/v1/home.json"

class Story {
    var headline: String?
    var thumbnailUrl: String?

    init(jsonResult: NSDictionary) {
       ...
    }

    class func fetchStories(successCallback: ([Story]) -> Void, error: ((NSError?) -> Void)?) {
        let manager = AFHTTPRequestOperationManager()
        manager.GET(resourceUrl, parameters: params, success: { (operation ,responseObject) -> Void in
            if let results = responseObject["results"] as? NSArray {
                var stories: [Story] = []
                for result in results as [NSDictionary] {
                    stories.append(Story(jsonResult: result))
                }
                successCallback(stories)
            }
        }, failure: { (operation, requestError) -> Void in
            if let errorCallback = error? {
                errorCallback(requestError)
            }
        })
    }
}
```

## Example: loading images asynchronously using UIImageView+AFNetworking
<!--- TODO: links to table view guide NYT example here -->
To demonstrate AFNetworking's integration with UIKit, we'll extend our
NY Times Viewer example from the table view guide to display the
thumbnail image associated with each headline.  We will use the
UIImageView+AFNetworking extension (category in Objective-C) to
asynchronously load the images.

<!--- TODO links to cocoapods guide here -->

We'll use CocoaPods to pull in AFNetworking.  Here's our `Podfile`

```
pod 'AFNetworking'
```

After a `pod install` we can open up `NYTimesViewer.xcworkspace`.  Since
we are working in Swift, we'll need to add an Objective-C bridging
header.  More information on how to do this can be found in the
CocoaPods guide.

<!--- TODO links to cocoapods guide here -->

```
#ifndef NYTimesViewer_BridgingHeader_h
#define NYTimesViewer_BridgingHeader_h

#import <MBProgressHUD.h>
#import <AFNetworking.h>
#import <UIImageView+AFNetworking.h>

#endif
```

Importing this extension (category) automatically ads extra
functionality to `UIImageView`.  Lastly, we only need to change a few
lines in our `StoryCell` code to load the remote images asynchronously.

```
import UIKit

class StoryCell: UITableViewCell {

    @IBOutlet weak var thumbnailView: UIImageView!
    @IBOutlet weak var headlineLabel: UILabel!

    var story: Story? {
        didSet {
            headlineLabel?.text = story?.headline
            headlineLabel?.sizeToFit()

            if let thumbnailUrl = story?.thumbnailUrl {
                thumbnailView?.setImageWithURL(NSURL(string: thumbnailUrl))
            }
        }
    }
}

```

<!--- TODO: insert image of table with thumbnails here-->

## Example: OAuth 1
_To be completed..._

## Example: OAuth 2
_To be completed..._
