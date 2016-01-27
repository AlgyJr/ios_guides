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

#### Parse Registration

If you have not already done so please [sign up for a Parse account.](https://www.parse.com/signup) While you are creating the account, go ahead and provide the name of the first application you are going to create with it. In this case we are calling it `InstaParse`.

#### Setting up Parse SDK on iOS application (using Cocoapods)
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

#### Adding your Parse app's `ApplicationId` and `ClientKey` to you iOS app

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

#### User Registration

One of the basic use cases for user management is to have them sign up. Once you have setup a view (in storyboard) and created a corresponding view controller (as shown in below image):

<img src="http://i.imgur.com/6kvrKOU.png" alt="Parse Quickstart" width="750"/>

Following code snippet shows how to sign up user:

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

#### User Login


## Reference

* Parse Documentation - https://parse.com/docs/ios/guide#getting-started

