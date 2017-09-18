Tab bars are one of the common types of navigation in iPhone apps, and they are easy to set up. Follow the steps below to set up a tab bar controller using both Storyboard and programatically.

<img src="http://i.imgur.com/omfSihU.gif" height="479" width="320" />

## Tab Bar Controllers in Storyboard

### Step 1: Drag a Tab Bar Controller in Storyboard

![Add tab bar to Storyboard|600](http://i.imgur.com/waWQjpP.gif)

### Step 2: Add the View Controllers

By default, dragging the tab bar view controller will also add two view controllers. You can also drag additional view controllers for a total of up to 5 view controllers.

![Add view controllers|600](http://i.imgur.com/OhHRlqs.gif)

### Step 3: Connecting the View Controllers

Each view controller in the tab bar controller need to be connected by ctrl-dragging from the tab bar controller to the view controller.

![Add view controllers|600](http://i.imgur.com/nDMhT3Y.gif)

### Step 4: Configuring the Tab Bar Image and Title

To configure the tab bar image and title, double-click on the view controller, and expand the view hierarchy view. Select the tab bar item and set the title and image in the properties pane.

![Setting image|600](http://i.imgur.com/iTh4VKO.gif)

## Tab Bar Controllers in Code

### Step 1: Create the tab bar controller

To create the tab bar controller in the image above, create a UITabBarController in the application delegate and set it to be the rootViewController of the window.

```swift
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {

        self.window = UIWindow(frame: UIScreen.main.bounds)

        // Create the tab bar controller
        let tabBarController = UITabBarController()

        self.window?.rootViewController = tabBarController
        self.window?.backgroundColor = UIColor.white
        self.window?.makeKeyAndVisible()
```

```objective_c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];

    // Create the tab bar controller
    UITabBarController *tabBarController = [[UITabBarController alloc] init];

    self.window.rootViewController = tabBarController;

    self.window.backgroundColor = [UIColor whiteColor];
    [self.window makeKeyAndVisible];
    return YES;
}
```

### Step 2: Set the view controllers

Create a view controller for each tab that you want. It's common to have 2, 3, or 5 tabs in a tab bar.

#### Example 1: Creating two view controllers

```swift
// Create the two view controllers
let firstViewController = FirstViewController()
let secondViewController = SecondViewController()

let firstNavigationController = UINavigationController.init(rootViewController: firstViewController)
let secondNavigationController = UINavigationController.init(rootViewController: secondViewController)

tabBarController.viewControllers = [firstViewController, secondViewController]
```

```objective_c
// Create the two view controllers
FirstViewController *firstViewController = [[FirstViewController alloc] init];
SecondViewController *secondViewController = [[SecondViewController alloc] init];

tabBarController.viewControllers = @[firstViewController, firstViewController];
```

#### Example 2: Creating two view controllers within navigation controllers

Note that it is common for each view controller to be contained within a navigation controller. Each view controller has their own navigation controller because each tab has its own navigation history.

```swift
// Create the two view controllers, each within a navigation controller
let firstNavigationController = UINavigationController.init(rootViewController: firstViewController)
let secondNavigationController = UINavigationController.init(rootViewController: secondViewController)

tabBarController.viewControllers = [firstNavigationController, secondNavigationController]

```

```objective_c
// Create the two view controllers, each within a navigation controller
FirstViewController *firstViewController = [[FirstViewController alloc] init];
UINavigationController *firstNavigationController = [[UINavigationController alloc] initWithRootViewController:firstViewController];

SecondViewController *secondViewController = [[SecondViewController alloc] init];
UINavigationController *secondNavigationController = [[UINavigationController alloc] initWithRootViewController:secondViewController];

tabBarController.viewControllers = @[firstNavigationController, secondNavigationController];
```

### Step 3: Configuring the Tab Bar Items

You can configure the title, image, and selected image of the tab bar item in each view controller. The snippet below demonstrates setting the title and icon of each of the tab bar items.

```swift
// Configure the titles and images of the tab bar items
firstNavigationController.tabBarItem.title = "First"
firstNavigationController.tabBarItem.image = UIImage(named: "House")

secondNavigationController.tabBarItem.title = "Second"
secondNavigationController.tabBarItem.image = UIImage(named: "Martini")
```

```objective_c
// Configure the titles and images of the tab bar items
firstNavigationController.tabBarItem.title = @"First";
firstNavigationController.tabBarItem.image = [UIImage imageNamed:@"House"];

secondNavigationController.tabBarItem.title = @"Second";
secondNavigationController.tabBarItem.image = [UIImage imageNamed:@"Martini"];
```

### Summary

Combine the 3 steps above to get a code snippet like the following:

```objective_c
#import "AppDelegate.h"
#import "FirstViewController.h"
#import "SecondViewController.h"

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];

    // Create the two view controllers, each within a navigation controller
    FirstViewController *firstViewController = [[FirstViewController alloc] init];
    UINavigationController *firstNavigationController = [[UINavigationController alloc] initWithRootViewController:firstViewController];
    firstNavigationController.tabBarItem.title = @"First";
    firstNavigationController.tabBarItem.image = [UIImage imageNamed:@"House"];

    SecondViewController *secondViewController = [[SecondViewController alloc] init];
    UINavigationController *secondNavigationController = [[UINavigationController alloc] initWithRootViewController:secondViewController];
    secondNavigationController.tabBarItem.title = @"Second";
    secondNavigationController.tabBarItem.image = [UIImage imageNamed:@"Martini"];

    // Configure the tab bar controller with the two navigation controllers
    UITabBarController *tabBarController = [[UITabBarController alloc] init];
    tabBarController.viewControllers = @[firstNavigationController, secondNavigationController];

    self.window.rootViewController = tabBarController;

    self.window.backgroundColor = [UIColor whiteColor];
    [self.window makeKeyAndVisible];
    return YES;
}
```
