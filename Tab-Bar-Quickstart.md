## Overview
<a href="http://imgur.com/WhoidE5"><img src="http://i.imgur.com/WhoidE5.gif" title="source: imgur.com" /></a>

Tab bars controllers provide a simple interface for a users to switch
between a set of view controllers.  This quickstart covers the basic
procedure for setting up a tab bar controller and setting up the view
controllers for each tab.

## Storyboard setup
### Step 1: Add tab bar controller as initial view controller
In Interface Builder drag a `Tab Bar Controller` from the Object Library
into your storyboard.  It will come preconfigured with two tabs
corresponding to two view controllers.  Many times you will want your
tab bar controller to be the initial view controller.  You can set this
by selecting the tab bar controller and ticking `Is Initial View
Controller`.

<a href="http://imgur.com/WgiEuBZ"><img src="http://i.imgur.com/WgiEuBZ.png" title="source: imgur.com" /></a>

### Step 2: Add a view controller for each tab
The tab bar controller is configured with two tabs by default.  You can
delete a tab by selecting the corresponding view controller and deleting
it the storyboard.  To add a tab, first drag a new `View Controller`
object to the storybard.  Next control-drag from the tab bar controller
to new view controller and select `view controllers` under `Relationship
Segue`.  Your tab bar controller will update with a new tab.

<a href="http://imgur.com/OOMB573"><img src="http://i.imgur.com/OOMB573.gif" title="source: imgur.com" /></a>

### Step 3: Customize the bar item for each tab
You can customize appearance and title of each button on the tab bar by
selecting the tab bar item **inside the corresponding view controller**.
In particular, you'll likely want to add an image for each tab bar item.

<a href="http://imgur.com/nZ2g4fp"><img src="http://i.imgur.com/nZ2g4fp.png" title="source: imgur.com" /></a>

### Step 4: Create view controller classes for tabs
You'll need to create classes to contain the code for the view
controllers corresponding to each tab.  Select `File -> New -> iOS ->
Source -> Cocoa Touch Class` and create a new subclass of
`UIViewController` for each kind of tab you will have.

### Step 5: Set custom class for and finish design of tabs
For each tab in your storyboard select the corresponding view controller
and set its custom class to one of the classes you just created.  You
can now add other components to this tab and connect outlets to your
view controller class as you would with any other view controller.

<a href="http://imgur.com/5YWL3Vw"><img src="http://i.imgur.com/5YWL3Vw.png" title="source: imgur.com" /></a>

## Programmatic setup

You can instantiate a tab bar controller programmatically and use it as
you would any other view controller.  If you need it to be your root view
controller the best place to do this is in the App Delegate. The example below creates a Tab Bar Controller with two simple View Controllers (each with a different background color).

```swift
// AppDelegate.swift

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    
    var window: UIWindow?
    
    func application(application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
            window = UIWindow(frame: UIScreen.mainScreen().bounds)
            
            // Set up the first View Controller
            let vc1 = UIViewController()
            vc1.view.backgroundColor = UIColor.orangeColor()
            vc1.tabBarItem.title = "Orange"
            vc1.tabBarItem.image = UIImage(named: "heart")
            
            // Set up the second View Controller
            let vc2 = UIViewController()
            vc2.view.backgroundColor = UIColor.purpleColor()
            vc2.tabBarItem.title = "Purple"
            vc2.tabBarItem.image = UIImage(named: "star")
            
            // Set up the Tab Bar Controller to have two tabs
            let tabBarController = UITabBarController()
            tabBarController.viewControllers = [vc1, vc2]
            
            // Make the Tab Bar Controller the root view controller
            window?.rootViewController = tabBarController
            window?.makeKeyAndVisible()
            
            return true
    }
    
    // ...
}
```

```
// AppDelegate.m

// ...

- (BOOL)application:(UIApplication *)application
didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    
    // Set up the first View Controller
    UIViewController *vc1 = [[UIViewController alloc] init];
    vc1.view.backgroundColor = [UIColor orangeColor];
    vc1.tabBarItem.title = @"Orange";
    vc1.tabBarItem.image = [UIImage imageNamed:@"heart"];
    
    // Set up the second View Controller
    UIViewController *vc2 = [[UIViewController alloc] init];
    vc2.view.backgroundColor = [UIColor purpleColor];
    vc2.tabBarItem.title = @"Purple";
    vc2.tabBarItem.image = [UIImage imageNamed:@"star"];
    
    // Set up the Tab Bar Controller to have two tabs
    UITabBarController *tabBarController = [[UITabBarController alloc] init];
    [tabBarController setViewControllers:@[vc1, vc2]];
    
    // Make the Tab Bar Controller the root view controller
    self.window.rootViewController = tabBarController;
    [self.window makeKeyAndVisible];
    
    return YES;
}

// ...
```

<a href="http://imgur.com/GhzQOTw"><img src="http://i.imgur.com/GhzQOTw.gif" title="source: imgur.com" /></a>

## Further reading
* [Official Tab Bar Guide from Apple][appleguide]

[appleguide]: https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/TabBarControllers.html