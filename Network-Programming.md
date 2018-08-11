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

[nsurlconnectionguide]: https://developer.apple.com/reference/foundation/nsurlconnection

**[NSURLSession][nsurlsessionguide]** a higher level library that is part
of Apple's Foundation Framework.

* built on top of NSURLConnection
* better support for authentication and has a session concept
* concept of "task" enables pausing/resuming requests
* can perform requests while your app is in the background
* does not handle parsing of common content types
* not much built in support for HTTP error codes / request parameters

[nsurlsessionguide]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/URLLoadingSystem/Articles/UsingNSURLSession.html

**[AFNetworking][afnetworking]** is the most popular library for and is
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

**[AlamoFire][alamofire]** is another networking library by the same
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

One simple way to ensure a block of code is run on the main thread using
Grand Central Dispatch is as follows  
  
**Objective-C**
```objc
dispatch_async(dispatch_get_main_queue(), {
  // this code will be executed on the main thread
})
```
**Swift**
```swift
DispatchQueue.main.async {  
  // This code will be executed on the main thread
}  
```

## Making a GET and parsing a JSON response
In order to provide you with the flavor of each of the major ways of
making network requests we discussed above, we'll go through an example of each one.

### NSURLConnection (deprecated in iOS 9)
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
                    // unexpected error happened
                    error?(nil)
                }
            }
        }
    }
}
```

### URLSession
[URLSession](https://developer.apple.com/library/ios/documentation/Foundation/Reference/NSURLSession_class) is now the preferred built-in method of performing network requests on iOS.

#### Swift

```swift
class Movie {

    // ... 

    class func fetchMovies(successCallBack: @escaping (NSDictionary) -> (), errorCallBack: ((Error?) -> ())?) {
        let apiKey = "Put_Your_Client_Id_Here"
        let url = URL(string: "https://api.themoviedb.org/3/movie/now_playing?api_key=\(apiKey)")!
        let request = URLRequest(url: url, cachePolicy: .reloadIgnoringLocalCacheData, timeoutInterval: 10)
        let session = URLSession(configuration: .default, delegate: nil, delegateQueue: OperationQueue.main)
        let task: URLSessionDataTask = session.dataTask(with: request) { (data: Data?, response: URLResponse?, error: Error?) in
            if let error = error {
                errorCallBack?(error)
            } else if let data = data,
                let dataDictionary = try! JSONSerialization.jsonObject(with: data, options: []) as? NSDictionary {
                //print(dataDictionary)
                successCallBack(dataDictionary)
            }
        }
        task.resume()
    }
    // ...
}
```

#### Objective-C
```objc
NSString *clientId = @"Put_Your_Client_Id_Here";
NSString *urlString =
[@"https://api.instagram.com/v1/media/popular?client_id=" stringByAppendingString:clientId];

NSURL *url = [NSURL URLWithString:urlString];
NSURLRequest *request = [NSURLRequest requestWithURL:url];

NSURLSession *session =
[NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                              delegate:nil
                         delegateQueue:[NSOperationQueue mainQueue]];

NSURLSessionDataTask *task = [session dataTaskWithRequest:request
                                        completionHandler:^(NSData * _Nullable data,
                                                            NSURLResponse * _Nullable response,
                                                            NSError * _Nullable error) {
                                            if (!error) {
                                                NSError *jsonError = nil;
                                                NSDictionary *responseDictionary =
                                                [NSJSONSerialization JSONObjectWithData:data
                                                                                options:kNilOptions
                                                                                  error:&jsonError];
                                                NSLog(@"Response: %@", responseDictionary);
                                            } else {
                                                NSLog(@"An error occurred: %@", error.description);
                                            }
                                        }];
[task resume];
```  

### Codable  
[Codable](https://developer.apple.com/documentation/swift/codable) is Apple's latest powerful contribution to efforts to better improve the built-in networking libraries available to iOS and Mac OS engineers. Codable is actually a typealias for [Encodable and Decodable](https://developer.apple.com/documentation/foundation/archives_and_serialization/encoding_and_decoding_custom_types) protocols that allows you to quickly decode and encode external representations (such as JSON strings) as native Structs in Swift.  
Before Codable was introduced to the Swift language in Swift 4, many developers had to rely on third party frameworks or building their own JSON decoding code which required a lot of boilerplate code. However, with the introduction of Codable, it's actually really easy to write 100% Swift networking code! Let's give it a try!  
  
Let's assume you are implementing a movie list viewing application that retrieves a list of movies from a server to show the user, with the following JSON response.  
```javascript  
{
    "totalFilms": 100,
    "films": [{
            "id": 11219,
            "image_url": "https://movieguru.org/sample1.png",
            "title": "The Hunt for Red October",
            "score": 4.5
        },
        {
            "id": 11169,
            "image_url": "https://movieguru.org/sample1.png",
            "title": "Wolf of Wall Street",
            "score": 5.0
        },
        {
            "id": 13671,
            "image_url": "https://movieguru.org/sample1.png",
            "title": "Superbad",
            "score": 4.2
        }
    ]
}
```  
With the power of Codable, you can implement a native Struct object with the following code.  
```swift  
struct MovieResponse: Codable {
    let totalFilms: Int
    let films: [Film]
}

struct Film: Codable {
    let id: Int
    let imageURL, title: String
    let score: Double

    enum CodingKeys: String, CodingKey {
        case id
        case imageURL = "image_url"
        case title, score
    }
}
```  
As you can see, the Struct looks almost exactly like you would want it to look if you were using it to drive a UITableViewDataSource or a custom film details View Controller.  
The [**CodingKey**](https://developer.apple.com/documentation/swift/codingkey) is simply an enum that allows the JSONDecoder to perform an internal switch on each key of the JSON response in order to match each key to the property name of the Struct. Can you guess why imageURL is the only case in the enum that has a declared raw value? If you're thinking it's related to **Snake Case**, you're right! While **Snake Case** works in Swift, it's not best practice, and Apple cleverly considered that an application might need properties to have different names compared to their network properties so an engineer can overwrite the property name by mapping a different variable name to each JSON parameter. If you wanted, you could make imageURL read "thumbnailURL" instead, as long as the encoding key is equal to "image_url", the JSONDecoder will know that the JSON value for key "image_url" is set to thumbnailURL.  
So how do we use it? Easy!  
Let's go back to the example from **URLSession**, and instead of a general NSDictionary (which would require a lot more code on the consumption side like a MovieObject class with init(fromDict dict: NSDictionary) in order to be usable in your code base), let's substitute it with our **Codable** compatible struct.  
  
```swift
class Movie {

    // ... 

    class func fetchMovies(successCallBack: @escaping ([Film]?) -> (), errorCallBack: ((Error?) -> ())?) {
        let apiKey = "Put_Your_Client_Id_Here"
        let url = URL(string: "https://api.themoviedb.org/3/movie/now_playing?api_key=\(apiKey)")!
        let request = URLRequest(url: url, cachePolicy: .reloadIgnoringLocalCacheData, timeoutInterval: 10)
        let session = URLSession(configuration: .default, delegate: nil, delegateQueue: OperationQueue.main)
        let task: URLSessionDataTask = session.dataTask(with: request) { (data: Data?, response: URLResponse?, error: Error?) in
            if let error = error {
                errorCallBack?(error)
            } else if let data = data,
                let filmResponse = try! JSONDecoder().decode(MovieResponse.self, from: data) {
                //print(filmResponse.films)
                successCallBack(filmResponse.films)
            }
        }
        task.resume()
    }
    // ...
}
```  
  
It might not look like much, but a significant amount of code is saved from the Movie class object, and when you consume this API call, on the other side you'll get the Film objects you need to drive your UI, rather than a Dictionary you'd have to iterate over, verifying each value.  
  
Codable can save you a significant amount of time in writing networking code, and it's growing in popularity, so I highly recommend picking it up!
  
### AFNetworking
This code starts to look a little cleaner with AFNetworking.
AFNetworking does some error handling for us and gives us a way to
provide a failure handler.  Also note that we no longer have to parse
the JSON result ourselves since AFNetworking does this automatically
based on the content type.  Finally note that we were able to supply our
GET parameters as a Swift dictionary.  This is not particularly useful
here, but becomes very nice to have if there is a large number of
parameters.

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
#### Objective-C
```objc
- (void)getNowPlaying:(void(^)(NSDictionary *))completion {

    NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
    AFURLSessionManager *manager = [[AFURLSessionManager alloc] initWithSessionConfiguration:configuration];
    
    NSURL *URL = [NSURL URLWithString:@"https://api.themoviedb.org/3/movie/now_playing?api_key=a07e22bc18f5cb106bfe4cc1f83ad8ed"];
    NSURLRequest *request = [NSURLRequest requestWithURL:URL];
    
    NSURLSessionDataTask *dataTask = [manager dataTaskWithRequest:request
                                                   uploadProgress:nil
                                                 downloadProgress:nil
                                                completionHandler:^(NSURLResponse *response, id  responseObject, NSError *error) {
                                                        if (error) {
                                                            NSLog(@"Error: %@", error);
                                                        } else {
                                                            NSLog(@"%@ %@", response, responseObject);
                                                            NSDictionary *dataDictionary = responseObject;
                                                            completion(dataDictionary);
                                                        }
                                                }];
    [dataTask resume];
}
```