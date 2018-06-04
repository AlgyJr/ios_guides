Any view controller may present another view controller with some animation, commonly sliding up from below or fading in. Of course, as in Inception, that view controller can present another view controller which can present another view controller and so on. However, the common use case is to present a single view controller that will be dismissed shortly, as depicted below.

<img src="https://i.imgur.com/4wESHoK.gif" width="250" height="443" />&nbsp;&nbsp;<img src="https://i.imgur.com/KpX7FcB.gif" width="250" height="443" />

This is a quick guide to presenting and dismissing view controllers in Storyboard.

###Presenting and dismissing view controllers using Storyboard

#### Step 1: Create the Segue

Ctrl-drag from the button to the view controller that you want to present modally, as shown below.

![Create Segue|700](https://i.imgur.com/HjGJq93.gif)

#### Step 2: Configure the Segue Animation Properties

Click on the segue in the Storyboard and open the Properties view to choose the animation type or disable animation.

![Configure Properties|700](https://i.imgur.com/HXCQTz5.gif)

####The Presentation and Transition properties
The presentation style of a view controller governs its appearance onscreen. Full screen presentation style covers the entire screen. The Page Sheet and Form Sheet styles allow portions of the underlying view controller to show through.

Below is an example of the built-in transition styles Cross Disolve, Partial Curl, Flip Horizontal & Cover Vertical:

| Cover Vertical  |  Partial Curl | Flip Horizontal  |  Cross Dissolve |  
|---|---|---|---|
| <img src="https://i.imgur.com/WPRmx5y.gif" width="250" height="443" />  |  <img src="https://i.imgur.com/DuU50x7.gif" width="250" height="443" /> | <img src="https://i.imgur.com/omFPemo.gif" width="250" height="443" />  |  <img src="https://i.imgur.com/k7iBM8D.gif" width="250" height="443" /> |

The standard transition styles for presenting the view controller can be supplemented with custom transitions of your own.

#### Triggering the Transition Manually

Sometimes, you want the transition to happen, but it's not the result of a button tap. In that case, you have to name the segue and then call the `performSegue` method in code. The name of the segue doesn't matter, but it should be unique for that view controller.

![Name Segue|700](https://i.imgur.com/m1SnyFw.gif)

Once, you've given the segue a unique name, you can call invoke it in code, which will trigger the modal transition.

```swift
performSegue(withIdentifier: "firstSegue", sender: nil)
```
```objc
[self performSegueWithIdentifier:@"showWebVC" sender:nil];
```

Below is an example of presenting a View Controller programmatically
![Present VC|700](https://i.imgur.com/5Bl9ze2.gif)


#### Dismissing Modal Transitions

The easiest way to dismiss a modal transition and return to the original view controller is to do it in code. Create a button that will dismiss the view controller and add a button action.

In the button action, call the method below to dismiss the current view controller.

```swift
dismiss(animated: true, completion: nil)
```
```objc
[self dismissViewControllerAnimated:YES completion:nil];
```
#### Presenting View Controllers Programmatically

Sometimes, you may want to present different View Controllers dynamically based on certain conditions in code or you may want to show a View Controller directly, without using the storyboard Segue.

To do so, first instantiate the View Controller using it's storyboard Id as below:

```swift
let mainStoryboard = UIStoryboard(name: "Main", bundle: nil)
let userVC = mainStoryboard.instantiateViewControllerWithIdentifier("userVC") as! UserViewController

```
```objc
UIStoryboard *storyboard = [UIStoryboard storyboardWithName:@"Main" bundle:nil];
WebViewController *webVC = [storyboard instantiateViewControllerWithIdentifier:@"WebVC"];
```

Then, present the View Controller using the following API

```swift
presentViewController(userVC, animated: true, completion: nil)

```
```objc
[self presentViewController:webVC animated:YES completion:nil];
```
### Passing Data

Often, you will need to pass data from a view controller to the view controller that is being presented. There is a special method that is called right before the segue happens. In that method, you can access the view controller that is being transitioned to and set the appropriate property.

In the source view controller, implement the following method. Often, you'll want to cast the destination view controller to the specific view controller so you can set the appropriate property.

```swift
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
	var destinationViewController = segue.destination as! PhotoViewController
        
	destinationViewController.image = self.imageView.image
}
```
```objc
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
    
    MovieCell *cell = (MovieCell *)sender;    
    NSDictionary *movie = cell.movie;
    
    DetailViewController *detailVC = segue.destinationViewController;    
    detailVC.movie = movie;
}
```
This assumes that the destination view controller has a property called `image`.

```swift
class PhotoViewController : UIViewController {
	var image: UIImage!

}

```
```objc
@interface DetailViewController : UIViewController
@property(strong, nonatomic) NSDictionary *movie;
```
Then, in the destination view controller, after the view loads, set the image property of the image view.

```swift
override func viewDidLoad() {
	super.viewDidLoad()

	imageView.image = image
}

```
```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.movieTitle.text = self.movie[@"title"];
    self.movieDescription.text = self.movie[@"overview"];
}
```