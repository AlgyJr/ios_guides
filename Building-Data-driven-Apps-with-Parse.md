## Overview

This page covers a range of topics to serve as a primer for building data-driven iOS applications with **Parse**. Some of the topics covered here are:

- Parse registration
- User Registration/Authentication
- Parse Querying
- Parse Relationships
- File Upload to Parse

#### What is Parse?

[Parse](https://parse.com/) is a service that allows app developers to effortlessly add a backend to their apps without worrying about server setup and maintenance. With some basic setup, one is able to get a database and RESTful API up and running. Additionally, it is easy to further add push notification and social integration capabilities in your app. 

[More about Parse on Quora](https://www.quora.com/What-is-Parse)

## Instagram App

In covering Parse SDK functionality, we will be using example of building an Instagram like application from ground up (i.e. with your own backend). In each of the following section we will following use cases to build a basic version of the application:

* User account creation, user login and sesssion persistence
* User can take a photo, add a caption, and post it to "Instagram"
* User can view the last 20 posts submitted to "Instagram"

The final application should look something like following:

<img src="http://i.imgur.com/EjhojU6.gif" alt="Instangram app demo"/>

### Architecture Overview:

Below is a quick overview of the app architecture:

<img src="http://i.imgur.com/Xo47jSc.gif" alt="Parse" width="750" />

1. To fetch the data from the cloud (Parse server) you will be using `PFQuery` ([More details](http://guides.codepath.com/ios/Building-Data-driven-Apps-with-Parse#parse-data-objects-pfobject-parse-files-pffile))
1. To add data to cloud you will be using `PFObject` and it's methods. ([More details](http://guides.codepath.com/ios/Building-Data-driven-Apps-with-Parse#fetching-data-from-parse-via-pfquery))

### Data Schema Design

An important aspect of the any data driven application is to be able to design the data schema. This helps in data organization so that related data is stored in to a single table or class (in `Parse` terminology) and non-related data is stored into different tables or classes.

For this app, we will be using just 1 additional data class (represented by `PFObject`) apart from `_User` class that Parse SDK created when first user is registered. `_User` is specifically reserved for `PFUser` and we can not change the class name for `PFUser`. However, we do get to name other classes that store the data. This class is will be called `Post` and will have following fields:

```
1. objectId: unique id for the user post (default field)
2. author: image author
3. image: image that user posts
4. caption: image caption by author
5. commentsCount: number of comments that has been posted to an image
6. likesCount: number of likes for the post
7. createdAt: date when post is created (default field)
8. updatedAt: date when post is last updated (default field)
9. ACL: object permissions (default field) (out of the scope for this tutorial)
```
*default field - created automatically by parse SDK, no need to explicitly specify in `PFObject` for the class

Here is how data model would look like: (`field_name`: `field_type`)

<img src="http://i.imgur.com/ekvdstz.gif" alt="Data Model"/>



`Post` and `_User` data classes should be enough to store data based on use cases mentioned above.

## Getting started with Parse

Parse used to provide a hosted version of their backend service with free registration. But as of February 2016, Parse has stopped new account registration as they have announced that the service will be shutdown on January 28th, 2017. They have open sourced their server code which you can host by yourself and use it as the backend to your application.

Please refer to this guide for instructions on how to [host and configure your own Parse server](https://guides.codepath.com/ios/Configuring-a-Parse-Server).

See this section on the [client SDK integration](https://guides.codepath.com/ios/Configuring-a-Parse-Server#enabling-client-sdk-integration).

## Parse User (`PFUser`)

Parse provides a specialized user class called `PFUser` that automatically handles much of the functionality required for user account management. With this class, you'll be able to add user account functionality in your app.

##### Properties

`PFUser` has several properties but following are the most important ones:

* **username**: The username for the user (required).
* **password**: The password for the user (required on signup).
* **email**: The email address for the user (optional).

### User Registration

One of the basic use cases for user management is to have them sign up. Once you have setup a view (in storyboard) and created a corresponding view controller (as shown in below image):

<!--a target="_blank" href="http://i.imgur.com/6kvrKOU.png"><img src="http://i.imgur.com/6kvrKOU.png" alt="Parse User Sign Up" width="750"/></a-->

The following code snippet shows how to sign up user:

```swift
   func registerUser() {
        // initialize a user object
        let newUser = PFUser()

        // set user properties
        newUser.username = usernameLabel.text
        newUser.email = emailLabel.text
        newUser.password = passwordLabel.text

        // call sign up function on the object
        newUser.signUpInBackground { (success: Bool, error: Error?) in
            if let error = error {
                print(error.localizedDescription)
            } else {
                print("User Registered successfully")
                // manually segue to logged in view
            }
        }   
    }
```

###### Notes:
1. `signUpInBackgroundWithBlock` signs up the user *asynchronously*. This will also enforce that the username isn't already taken. (Warning: Make sure that password and username are set before calling this method.)

   Parameters:
   `block` - The block to execute. 

[Parse User Sign-Up documentation](https://parse.com/docs/ios/guide#users-signing-up).

### User Login

Once the user has signed up, next step is to have them log in to your app. The image below shows the Xcode setup to do the same, followed by code snippet for user login (note that the username you enter is case sensitive).

<!--a target="_blank" href="http://i.imgur.com/DEKJjP0.png"><img src="http://i.imgur.com/DEKJjP0.png" alt="Parse User Login" width="750"/></a-->

```swift
func loginUser() {

   let username = usernameLabel.text ?? ""
   let password = passwordLabel.text ?? ""

   PFUser.logInWithUsername(inBackground: username, password: password) { (user: PFUser?, error: Error?) in
        if let error = error {
          print("User log in failed: \(error.localizedDescription)")
        } else {
          print("User logged in successfully")
          // display view controller that needs to shown after successful login
        }
     }   
}
```
###### Notes:
1. `??` used in above code snippet is a <a href="http://guides.codepath.com/ios/Understanding-Swift#understanding-the-question-mark" target="_blank">*nil coalescing operator*</a>
2. `logInWithUsernameInBackground` - Makes an *asynchronous* request to log in a user with specified credentials.
Returns an instance of the successfully logged in `PFUser`. This also caches the user locally so that calls to `PFUser.current()` will use the latest logged in user.

 - Parameters:
    - username: The username of the user.
    - password: The password of the user.
    - block: The block to execute. 

[Parse User Login documentation](https://parse.com/docs/ios/guide#users-logging-in).

### Persisting user session

Once a user successfully logs into your application, Parse caches the logged in user object for convenient access throughout your application. `PFUser.current()` can be used to get the current user. You can use this functionality in `AppDelegate` to check if there is a current user in Parse cache or not. If there is a cached user already present then you can directly load the Home view controller (without asking the user to login again).

```swift
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {

        // Code to initialize Parse
        // (See above section 'Parse `initializeWithConfiguration` vs `setApplicationId`', if you have not already set it up)

        // check if user is logged in.
        if PFUser.current() != nil {
            // if there is a logged in user then advance to the authenticated view controller
        }

        return true
    }
```
### User Logout

You can clear the current user by calling following class function on `PFUser`:

```swift
PFUser.logOutInBackgroundWithBlock { (error: NSError?) in
// PFUser.current() will now be nil     
}
```
The above code should be added to the action associated with the logout button (or any other event needs to log out current user)

## Parse Data Objects (`PFObject`) & Parse Files (`PFFile`)

### `PFObject`
Storing data on Parse is built around the `ParseObject`. Each `ParseObject` contains key-value pairs of JSON-compatible data. This data is schemaless, which means that you don't need to specify ahead of time what keys exist on each `ParseObject`. You simply set whatever key-value pairs you want, and Parse backend will store it.

#### Creating Parse Objects

Each `ParseObject` has a class name that you can use to distinguish different sorts of data. For example, in case of our application, we might call `ParseObject` to store uploaded images with name `Post`:

```swift
import Parse

var shield = PFObject(className: "Armor")
shield["displayName"] = "Wooden Shield"
shield["fireProof"] = false
shield["rupees"] = 50
```

#### Subclassing PFObject

You can also declare models that can be later used by using [native subclasses](http://blog.parse.com/announcements/stay-classy-objective-c-introducing-native-subclasses-for-parse-objects/), which help for autocomplete checks. In this case, we need to set the fields/properties ahead of time and annotating with the `@NSManaged` property:

```swift
// needs to be imported 
import Parse

// subclassing PFObject will automatically register with Parse SDK now
// See https://github.com/parse-community/Parse-SDK-iOS-OSX/pull/967
class Armor: PFObject, PFSubclassing {
    // properties/fields must be declared here
    // @NSManaged to tell compiler these are dynamic properties
    // See https://stackoverflow.com/questions/31357564/what-does-nsmanaged-do
    @NSManaged var displayName: String?
    // Objective C has no concept of optionals 
    @NSManaged var fireProof: Bool
    @NSManaged var rupees: Int

    // returns the Parse name that should be used
    class func parseClassName() -> String {
        return "Armor"
    }
}
```

Instantiating an Armor class then looks like:

```swift
let shield = Armor()
shield.displayName = "Wooden Shield"
shield.fireProof = false
shield.rupees = 50
```

#### Saving

We can save simply by calling `saveInBackground()`:
```swift
shield.saveInBackground()
```

We can also pass a completion block handler to check the success/failure of the save operation:
```swift
shield.saveInBackground(block: { (success, error) in
   if (success) {
    // The object has been saved.
  } else {
    // There was a problem, check error.description
  }
})
```

### Live Queries

First, we need to add the [ParseLiveQuery](https://github.com/parse-community/ParseLiveQuery-iOS-OSX#cocoapods) to our `Podfile`:

```ruby
pod 'ParseLiveQuery'
```

Next, we need to instantiate a websocket client using the `ParseLiveQuery.Client`.  We also need to create a subscription to events that may be triggered by the backend.   Both the client and subscription should be declared outside lifecycle methods so they are not [auto released](https://github.com/parse-community/ParseLiveQuery-iOS-OSX/issues/105#issuecomment-304708482).  Otherwise, no subscription events will be triggered.

```swift
// make sure to import module at the top
import ParseLiveQuery

class ViewController: UIViewController {

   // make sure to declare both client and subscriptions variables outside lifecycle methods
   // otherwise, websocket delegate methods won't fire
    var client : ParseLiveQuery.Client!
    var subscription : Subscription<Message>!
 
    override func viewDidLoad() {
      var armorQuery: PFQuery<Armor> {
        return (Armor.query()!
                     .whereKeyExists("displayName")
                     .order(byAscending: "createdAt")) as! PFQuery<Armor>
      }
      client = ParseLiveQuery.Client()
      subscription = client.subscribe(armorQuery)
                    // handle creation events, we can also listen for update, leave, enter events
                           .handle(Event.created) { _, armor in
                              print("\(armor.displayName)")
                           }
   }  
```

Note that the `Message` class must be designated by the Parse back-end to support live queries.  See [this section](https://guides.codepath.com/ios/Configuring-a-Parse-Server#adding-support-for-live-queries) on how to do so.

### `PFFile`
`PFFile` lets you store application files in the cloud that would otherwise be too large or cumbersome to fit into a regular `PFObject`. The most common use case is storing images but you can also use it for documents, videos, music, and any other binary data (up to 10 megabytes).

[Parse Documentation on handling Images using `PFFile`](https://parse.com/docs/ios/guide#files-images)

### (Use Case) Post photos to Instagram (Parse)

In this example, we will create and save an object to Parse for an image that the user wants to upload along with some other details. Let's create a `model` class for `Post` object. We will use this model as a wrapper around PFObject to encapsulate CRUD functionality from the ViewControllers.

```swift
    class Post: PFObject, PFSubclassing {
        @NSManaged var media : PFFile
        @NSManaged var author: PFUser
        @NSManaged var caption: String
        @NSManaged var likesCount: Int
        @NSManaged var commentsCount: Int
 
        /* Needed to implement PFSubclassing interface */
        class func parseClassName() -> String {
          return "Post"
        }

        /**
         * Other methods
         */

        /**
        Method to add a user post to Parse (uploading image file)
     
        - parameter image: Image that the user wants upload to parse
        - parameter caption: Caption text input by the user
        - parameter completion: Block to be executed after save operation is complete
         */
        class func postUserImage(image: UIImage?, withCaption caption: String?, withCompletion completion: PFBooleanResultBlock?) {
            // use subclass approach
            let post = Post()

            // Add relevant fields to the object
            post.media = getPFFileFromImage(image) // PFFile column type
            post.author = PFUser.current() // Pointer column type that points to PFUser
            post.caption = caption
            post.likesCount = 0
            post.commentsCount = 0

            // Save object (following function will save the object in Parse asynchronously)
            post.saveInBackgroundWithBlock(completion)
        }

        /**
        Method to convert UIImage to PFFile
     
        - parameter image: Image that the user wants to upload to parse

        - returns: PFFile for the the data in the image
         */
        class func getPFFileFromImage(image: UIImage?) -> PFFile? {
            // check if image is not nil
            if let image = image {
                // get image data and check if that is not nil
                if let imageData = UIImagePNGRepresentation(image) {
                      return PFFile(name: "image.png", data: imageData)
                }
            }
            return nil
        }
    }
```
###### Notes:
1. To upload the user image to Parse, get the user input from the view controller and then call the `postUserImage` method from the view controller by passing all the required arguments into it (Please see method's comments for more details on arguments).
2. Please refer to <a href="https://github.com/codepath/ios_guides/wiki/Camera-Quickstart" target="_blank">Camera Quickstart</a> to find more details on how to get images using your device's Camera or Photo Library.

## Fetching data from Parse (via `PFQuery`)

`PFQuery` is used to retrieve data that is stored in Parse. The simplest way to do this is when you have the `objectId`. This is an asynchronous method, with variations for using either blocks or callback methods:

```swift
var query = Post.query()

query.getObjectInBackgroundWithId("imkmJsHVIH") {
  (post: PFObject?, error: NSError?) -> Void in
  if error == nil && gameScore != nil {
    print(post)
  } else {
    print(error)
  }
}
// The getObjectInBackgroundWithId methods are asynchronous, so any code after this will run
// immediately.  Any code that depends on the query result should be moved
// inside the completion block above.
```

#### Query constraints (filter, order, group the data)

Adding constraints to `PFQuery` can be done by either specifying `NSPredicate` or by using methods provided by `PFQuery`

##### Using `NSPredicate`

A `NSPredicate` can be passed to `PFQuery` constructor to specify query constraints. Below example shows how to construct a query to fetch all instagram posts with more than 100 likes on them.

```swift
// construct query
let predicate = NSPredicate(format: "likesCount > 100")
var query = Post.query(with: predicate)

// fetch data asynchronously
query.findObjectsInBackground { (posts: [Post]?, error: Error?) in
    if let posts = posts {
        // do something with the array of object returned by the call
        for post in posts {
            // access the object as a dictionary and cast type
            let likeCount = post.likesCount     
        }
    } else {
        print(error?.localizedDescription)
    }
}
```

A complete list of features supported by Parse for `NSPredicate` can be found [here](https://parse.com/docs/ios/guide#queries-specifying-constraints-with-nspredicate).

##### Using `PFQuery Methods`
There are several other methods that `PFQuery` provides to support SQL-like querying of objects. For example, you can get 20 instagram posts that have more than 100 likes on the post with following code:

```swift
// construct query
let query = Post.query()
query.whereKey("likesCount", greaterThan: 100)
query.limit = 20

// fetch data asynchronously
query.findObjectsInBackground { (posts: [Post]?, error: Error?) in
    if let posts = posts {
        // do something with the array of object returned by the call
    } else {
        print(error?.localizedDescription)
    }
}
```

For more examples and list of other methods supported by `PFQuery` for specifying constraints can be found [here](https://parse.com/docs/ios/guide#queries-query-constraints).

#### `Pointer` type fields and fetching their value (getting value of the object)

If one of the keys in your `PFObject` refers to another `PFObject` (note that `PFUser` is a sub-class of `PFObject`) then that field is of `Pointer` type. For example, in `Post` object which represents an Instagram post, one field that you would want to store is the author of the post. You can do this by assigning the current user to the `author` key when saving the post.

```swift
let post = Post()

// get the current user and assign it to "author" field. "author" field is now of Pointer type
post.author = PFUser.current() 
```

By default, when you fetch a `Post` object it won't have the author information. In order to get the information for the "author" you will have to use `PFQuery` method `includeKey`.

```swift
query.includeKey("author")
```

Please see below example for more context.

### (Use Case) View the last 20 posts submitted to "Instagram"

Based on above discussion, we can easily construct a `PFQuery` to fetch most recent posts from Parse as following:

```swift
// construct PFQuery
let query = Post.query()
query.orderByDescending("createdAt")
query.includeKey("author")
query.limit = 20

// fetch data asynchronously
query.findObjectsInBackgroundWithBlock { (posts: [Post]?, error: NSError?) -> Void in
    if let posts = posts {
        // do something with the data fetched
    } else {
        // handle error
    }
}
```

## `ParseUI`

`ParseUI` is a collection of a handy user interface components to be used with Parse iOS SDK, which streamline and simplify logging in/signing up PFUsers and displaying a list of PFObjects.

### Adding `ParseUI` cocoapod

Add `pod 'ParseUI'` under target in your Podfile and run `pod install`. For more detailed instructions see this <a href="http://guides.codepath.com/ios/CocoaPods#adding-a-pod" target="_blank">link</a>.

### Displaying images via `PFImageView`

Many apps need to display images stored in the Parse Cloud as `PFFile`s. However, to load remote images with the built-in `UIImageView` involves writing many lines of boilerplate code. `PFImageView` simplifies this task by abstracting away these parts.

Following code snippet show how to use a `PFImageView` to display images stored as `PFFile`

```swift
import UIKit
import Parse
import ParseUI

class InstagramPostTableViewCell: UITableViewCell {

    @IBOutlet weak var photoView: PFImageView!

    var instagramPost: PFObject! {
        didSet {
            self.photoView.file = instagramPost["image"] as? PFFile
            self.photoView.loadInBackground()
        }
    }
}
```

##### Notes
1. **Make sure to import `ParseUI`** wherever you want to use it's components
2. Make sure that you have set the `UIImageView` class to `PFImageView` in Interface Builder
<img src="http://i.imgur.com/ZV0BMn6.gif" alt="Parse Quickstart" width="750"/>

## Push Notifications

Push notifications let your application notify a user of new messages or events even when the user is not actively using your application.

#### How does push notification work?
[This tutorial](https://www.raywenderlich.com/32960/apple-push-notification-services-in-ios-6-tutorial-part-1) at raywenderlich.com provides a good introduction on how push notification works.

#### Enabling push notification 
**Note**: This section is only applicable if you have deployed your own Parse server.

Please refer to [this section](http://guides.codepath.com/ios/Configuring-a-Parse-Server#enabling-push-notifications) in `Configuring a Parse Server` guide for detailed steps on enabling push notifications on your server.

## Debugging

If you need to troubleshoot whether network calls to the Parse server, you can monitor the requests and responses.  First, add a few custom notification types:

```swift
extension Notification.Name {
    static let ParseWillSendURLRequestNotification = Notification.Name(rawValue: "PFNetworkWillSendURLRequestNotification")
    static let ParseDidReceiveURLResponseNotification = Notification.Name(rawValue: "PFNetworkDidReceiveURLResponseNotification")
}
```

Enable Parse debugging logs and add notification observers in your application delegate:

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {

  // set init log level
  Parse.setLogLevel(PFLogLevel.debug)

  NotificationCenter.default.addObserver(self, selector: #selector(receiveWillSendURLRequestNotification), name: Notification.Name.ParseWillSendURLRequestNotification, object: nil)
  NotificationCenter.default.addObserver(self, selector: #selector(receiveDidReceiveURLResponseNotification), name: Notification.Name.ParseDidReceiveURLResponseNotification, object: nil)
```

Add these functions to listen to notifications:

```swift
@objc func receiveWillSendURLRequestNotification(notification: Notification) {
   let request = notification.userInfo?[PFNetworkNotificationURLRequestUserInfoKey];

   if let nsRequest = request as? NSMutableURLRequest {
      print("URL: \(nsRequest.url?.absoluteString)!")
   }
}

@objc func receiveDidReceiveURLResponseNotification(notification: Notification) {
   let response = notification.userInfo?[PFNetworkNotificationURLResponseUserInfoKey];
   let responseBody = notification.userInfo?[PFNetworkNotificationURLResponseBodyUserInfoKey]

   if let response = response as? HTTPURLResponse {
       print ("Status Code: \(response.statusCode)")
   } else {
       return
   }

  if let responseBody = responseBody as? String {
     print ("Response Body: \(responseBody)")
  }
}
```

## FAQ

1)  Is there a way to put some key into an app (binary which is delivered from AppStore) and be completely secure?

Please refer to this comprehensive <a href="http://stackoverflow.com/a/14865695" target="_blank">explanation on Stack Overflow</a>


## Reference

* [Parse Documentation](http://docs.parseplatform.org/ios/guide/#getting-started)
* [Parse UI Cocoapods](https://cocoapods.org/pods/ParseUI)