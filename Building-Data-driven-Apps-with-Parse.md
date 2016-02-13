## Overview

This page covers a range of topics to serve as a primer for building data driven iOS applications with Parse. Some of the topics covered here are:

- Parse registration
- User Registration/Authentication
- Parse Querying
- Parse Relationships
- File Upload to Parse

#### What is Parse?

[Parse](https://parse.com/) is a service that allows app developers to effortlessly add a backend to their apps without worrying about server setup and maintenance. With some basic setup one is able to get a database and RESTful API up and running. Additionally, it is easy to further add push notification and social integration capabilities in your app. 

[More about Parse on Quora](https://www.quora.com/What-is-Parse)

## Getting started with Parse

### Parse Registration

If you have not already done so please [sign up for a Parse account.](https://www.parse.com/signup) While you are creating the account, go ahead and provide the name of the first application you are going to create with it. In this case we are calling it `InstaParse`.

### Setting up Parse SDK in iOS application (using Cocoapods)
(Assumes that you have already [setup CocoaPods](http://guides.codepath.com/ios/CocoaPods) on your Mac)

1. Create a new iOS project (`Single View Application`)
2. In Terminal, navigate to your project folder and create the `Podfile`.
3. Next edit your pod file to add `pod 'Parse'` under target.
      ```ruby
      # Podfile
      platform :ios, '9.0'

      use_frameworks!

      target 'InstaParse' do
         pod 'Parse'
      end
      ```
4. Setup Parse SDK by executing `pod install` command in terminal.
5. After Pod install is complete, open the project workspace created in step (4)

### Adding your Parse app's `ApplicationId` and `ClientKey` to you iOS app

1. Get the application id and key from your [Parse App's dashboard](https://www.parse.com/apps),
   - Click `quickstart guide` for the app that you want to use with your iOS app.
   - Navigate to instructions for setting up the application id and key. (`Data` > `Mobile` > `iOS` > `Swift` > `New Project`) 
<img src="http://i.imgur.com/vEP5TbZ.gif" alt="Parse Quickstart" width="750"/>

2. In your Xcode project, make following edits to project's `AppDelegate.swift` to add the id and key to your app:
   - Import Parse by adding `import Parse` in the beginning of the file
   - Add your Parse application id and key in `application:didFinishLaunchingWithOptions` function by copying the code `Parse.setApplicationId("<your-application-id>", clientKey: "your-client-key")`from Parse

      ```swift
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        // Override point for customization after application launch.
        Parse.setApplicationId("pZRoLzE1EXdcfaiMwvL3frpHHv3pIVClD6RFw8LI",**
            clientKey: "ZkiuDctukNhPjIOwXvpYiHW8hQ2ywds843AixEPm")**
        return true
    }
      ```

## Parse User (`PFUser`)

Parse provides a specialized user class called `PFUser` that automatically handles much of the functionality required for user account management. With this class, you'll be able to add user account functionality in your app.

##### Properties

`PFUser` has several properties but following are the most important ones:

* **username**: The username for the user (required).
* **password**: The password for the user (required on signup).
* **email**: The email address for the user (optional).

### User Registration

One of the basic use cases for user management is to have them sign up. Once you have setup a view (in storyboard) and created a corresponding view controller (as shown in below image):

<a target="_blank" href="http://i.imgur.com/6kvrKOU.png"><img src="http://i.imgur.com/6kvrKOU.png" alt="Parse User Sign Up" width="750"/></a>

use following code snippet that shows how to sign up user:

```swift
    @IBAction func registerUser(sender: UIButton) {
        # initialize a user object
        let newUser = PFUser()

        # set user properties
        newUser.username = usernameLabel.text
        newUser.email = emailLabel.text
        newUser.password = passwordLabel.text

        # call sign up function on the object
        newUser.signUpInBackgroundWithBlock { (success: Bool, error: NSError?) -> Void in
            if let error = error {
                print(error.localizedDescription)
            } else {
                print("User Registered successfully")
                self.dismissViewControllerAnimated(true, completion: nil)
            }
        }
        
    }
```
`signUpInBackgroundWithBlock` signs up the user *asynchronously*. This will also enforce that the username isn't already taken. (Warning: Make sure that password and username are set before calling this method.)

Parameters:
`block` - The block to execute. It should have the following argument signature: `^(BOOL succeeded, NSError *error)`.

[Parse User Sign-Up documentation](https://parse.com/docs/ios/guide#users-signing-up).

### User Login

Once the user has signed up, next step would to have them log in to you app. The image below shows the Xcode setup to do the same, followed by code snippet for user login (note that the username you enter is case sensitive).

<a target="_blank" href="http://i.imgur.com/DEKJjP0.png"><img src="http://i.imgur.com/DEKJjP0.png" alt="Parse User Login" width="750"/></a>

```swift
    @IBAction func loginUser(sender: UIButton) {

        let username = usernameLabel.text ?? ""
        let password = passwordLabel.text ?? ""

        PFUser.logInWithUsernameInBackground(username, password: password) { (user: PFUser?, error: NSError?) -> Void in
            if let error = error {
                print("User login failed.")
                print(error.localizedDescription)
            } else {
                print("User logged in successfully")
                // display view controller that needs to shown after successful login
            }
        }
        
    }
```

1. `??` used in above code snippet is a *nil coalescing operator* - (`a ?? b`) unwraps an optional `a` if it contains a value, or returns a default value `b` if a is nil. The expression `a` is always of an optional type. The expression `b` must match the type that is stored inside `a`.
2. `logInWithUsernameInBackground` - Makes an *asynchronous* request to log in a user with specified credentials.
Returns an instance of the successfully logged in `PFUser`. This also caches the user locally so that calls to `PFUser.currentUser()` will use the latest logged in user.

 - Parameters:
    - username: The username of the user.
    - password: The password of the user.
    - block: The block to execute. It should have the following argument signature: `^(PFUser *user, NSError *error)`.

[Parse User Login documentation](https://parse.com/docs/ios/guide#users-logging-in).

#### Persisting user session via `PFUser.currentUser()`

Once a user successfully logs in to your application, Parse caches the logged in user object for convenient access through out your application. `PFUser.currentUser()` can be used to get the current user. You can use this functionality in `AppDelegate` to check if there is a current user in Parse cache or not. If there is a cached user already present then you can directly load the Home view controller (without asking user to login again).

```swift
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        // setup parse keys
        Parse.setApplicationId("pZRoLzE1EXdcfaiMwvL3frpHHv3pIVClD6RFw8LI",
            clientKey: "ZkiuDctukNhPjIOwXvpYiHW8hQ2ywds843AixEPm")

        // check if the user is cached.
        if let _ = PFUser.currentUser() {
            // if the user is cached then load the home view controller
        }

        return true
    }
```


## Parse Data Objects (`PFObject`) & Parse Files (`PFFile`)

#### `PFObject`
Storing data on Parse is built around the `ParseObject`. Each `ParseObject` contains key-value pairs of JSON-compatible data. This data is schemaless, which means that you don't need to specify ahead of time what keys exist on each `ParseObject`. You simply set whatever key-value pairs you want, and Parse backend will store it.

Each `ParseObject` has a class name that you can use to distinguish different sorts of data. For example, in case of our application we might call `ParseObject` to store uploaded images with name `UserMedia`

#### `PFFile`
`PFFile` lets you store application files in the cloud that would otherwise be too large or cumbersome to fit into a regular `PFObject`. The most common use case is storing images but you can also use it for documents, videos, music, and any other binary data (up to 10 megabytes).

### (Use Case) Post photos to Instagram (Parse)

In this example, we will create and save an object to Parse for image that the user wants to upload along with some other details. Let's create a `model` class for `UserMedia` object. We will use this model as a wrapper around PBObject to encapsulate CRUD functionality from the ViewControllers.

```swift
    class UserMedia: NSObject {
        /**
         * Other methods
         */

        /**
        Method to post user media to Parse by uploading image file
     
        - parameter image: Image that the user wants upload to parse
        - parameter caption: Caption text input by the user
        - parameter completion: Block to be executed after save operation is complete
         */
        class func postUserImage(image: UIImage?, withCaption caption: String?, withCompletion completion: PFBooleanResultBlock?) {
            // Create Parse object PFObject
            let media = PFObject(className: UserMedia.ObjectName)

            // Add relevant fields to the object
            media["media"] = getPFFileFromImage(image) // PFFile column type
            media["author"] = PFUser.currentUser() // Pointer column type that points to PFUser
            media["caption"] = caption
            media["likesCount"] = 0
            media["commentsCount"] = 0

            // Save object (following function will save the object in Parse asynchronously)
            media.saveInBackgroundWithBlock(completion)
        }
```

## Reference

Parse Documentation - https://parse.com/docs/ios/guide#getting-started