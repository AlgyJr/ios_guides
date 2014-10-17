This is a tutorial that shows how to implement an app that display movies that are currently being played at theaters using the RottenTomatoes API

# The App
The app is composed of two screens. The first screen "Movies" is the root view, and shows a list of movies, in which, each movie is described by its title, synopsis, and a poster image. After a user selects a movie from the list, a second screen appears displaying additional details about the movie, including, the cast, and rating. 
Below are screenshots:

**Movies screen**
<br/>  

<a href="http://imgur.com/4Hw8FCA"><img src="http://i.imgur.com/4Hw8FCA.png" title="source: imgur.com" /></a>
<br/>  
**Movie Detail screen**
<br/>
<a href="http://imgur.com/2gkxCHk"><img src="http://i.imgur.com/2gkxCHk.png" title="source: imgur.com" /></a>
<br/>

## Steps to build app
The following is high level steps to follow to build the app. We will detail what goes in each step in the next section
### Add AIFNetworking library 
1. Create an Xcode Project with SingleView application template
2. Create a Podfile and install AFNetworking library

### Movies Screen
3. Add a TableViewController to display list of movies
4. Create custom cell 
5. Bind custom cell to source code via a UITableViewCell class 
6. Use RottenTomatoes API to request movies data
7. Parse data and use it to display title, synopsis and image in cell

### Movie Detail Screen 
8. Add a ViewController to display details of selected movie

### Transition from Movies to Movie Detail screen
9.  Add Segue method to transition 
10.  Create a Movie class to store information to pass to Movie Detail ViewController
11. Display passed movie data in Movie Detail screen


## Steps in detail

### Add AFNetworking library
####1.Create an Xcode Project with SingleView application template
* Create the Xcode project:
  * Create an Xcode project using the SingleView template, call it RottenTomatoes 
  * Close the project

####2.Create Podfile and run it to add the AFNetworking library
Cocoapods is a tool to install libraries into our Xcode project. We will use this tool to add the AFNetworking library as part of our Xcode project. If you have not yet installed Cocoapods follow these steps. For detail information on how to install Cocoapods go to http://cocoapods.org/.
*Install Cocoapods
  * Run these gem commands to install Cocoapods: 
    * `gem install cocoapods --user-install`
  * To check where Cocoapods was installed do:
    * `gem which cocoapods`
* Cocoapods is a ruby based tool, so you need to have Ruby working. If you encounter Ruby errors and want to re-install ruby, here is a step-by-step article on how to do that using homebrew: [re-installing Ruby ](https://www.computersnyou.com/2673/install-ruby-rails-macos-x-mavericks-10-9-mysql-step-step/)

* Create a Podfile and install AFNetworking library:
  * Cocopods reads commands from a file called Podfile that specify a target platform and what libraries to install in the Xcode project. We will create this file next.
  * In a terminal, cd down to your Xcode project directory, the one containing the file *.xcodeproj
  * Inside this directory, create blank Podfile by running: `touch Podfile`. This command will create the Podfile file. 
  * Open the Podfile file with an editor and enter the following lines. The first line specifies the target OS platform and the second line says to install the latest AFNetworking library
    * platform :ios, '7.0'
    * pod 'AFNetworking'
  * Install the AFNetworking library in your Xcode project by running: 
    * `pod install`. This command tells Cocoapods to execute the commands inside the Podfile. 
    * After running this command, you will see a new file, *.xcworkspace inside your project directory
  * Open your project using *xcworkspace. From now on, instead of using the *.xcodeproj file to open your project you have to use the *.xcworkspace file. The reason is that *.xcworkspace has the meta information about the new "pod" libraries (AFNetworking in this case) that should be included in the compilation of the project. 

### Movies Screen
####3.Add a TableViewController to display list of movies
* Open your *.xcworkspace project 
* The Storyboard (canvas) in Interface Builder (IB) will show by default a ViewController. We are going to replace this with a TableViewController, so delete it.
* If the IB library is not open do press control-ALT-command-3 to open it. From the library, drag a TableViewController component into the Storyboard
*  Select the TableViewController, click on the Attributes Inspector and check "Is Initial View Controller"
*  Run the app and check that you see an empty table
* Create a TableViewController class to back the TableViewcontroller we just added with code. Call the Class "MoviesTableViewController", and as subClass select UITableViewController
* Bind the new class to the TableViewController: In IB, select the TableViewController and set is class to "MoviesTableViewController" in the Identity Inspector
*  Add a UINavigationController to the Storyboard to enable transitions between screens and make the TableViewController the root controller:
  * Select TableViewController in IB
  * In the Xcode Menu Bar select this path:   Editor->Embed IN->Navigation Controller. This will place a Navigation Controller in IB, make the TableViewController the root view of the Navigation stack and add a Navigation Bar at the top end of the TableViewController
  * Select the Navigation Item in the Outline View (the hierarchical view of nested objects in the dock), and in the Attributes inspector enter "Movies" in the Title box. This sets the title of the Movies viewcontroller, which will be displayed in the Navigation Bar.

####4.Create Custom Cell
IOS has 4 standard cell types with fixed positions of an image, and two labels. In most cases, this doesn't suffice, so it is recommended to always create your own custom cell, which will show how to do next.
* In IB, select the Table View, select Attribute Inspector and make sure that the Content box is set to "Dynamic Prototypes" as opposed to "Static Cells". This tells Xcode that the apps is not using a fixed number of cells (static) but rather that cells will be dynamically created with the the pattern of the custom cell we are about to create.
* In IB, select the Cell ( it is easier to select the Cell in the Outline View to the left of the canvas), select Attribute inspect and set the "Style" box to "Custom". This tells Xcode that we are creating a custom cell, as opposed to one of the 4 standard ones.
* In addition, in the Attributes Inspector with the Cell selected, set the Identifier box to "MyMovieCell". We will use this identifier later inside our code to dynamically request for IOS to create our custom cell and return a pointer to, so that we can load it with the movie content we want to display.
* From the IB library, drag a UIImageView and two UILabel controls inside the Cell. Place the UIImageView one the left side, one UILabel to the right of the image to be used for the movie title, and one UILabel below to be used for the movie synopsis.
* Make the UILabel used for synopsis at least 6 lines in width by selecting it, going to the Attributes Inspector and setting the Lines box to 6. Also set the Font to a small type size like 12.0. Your canvas with the NavigationController and MoviesTableViewController should look something like this:

<br/>
<a href="http://imgur.com/ICM83lz"><img src="http://i.imgur.com/ICM83lz.png" title="source: imgur.com" /></a>
<br/>

####5.Bind custom cell to source code via a UITableViewCell class
* Now in order to access the UIImage and UILabels from source code (because we need to load the custom cells with content we will retrieve from RottenTomatoes site) , we need to bind this Custom cell with an Objective-C Class:
  * Create a  new Cocoa Touch Class with Class name MovieCell and Sub-class equal to UITableViewCell with NO XIB (since we already created this in the TableView prototype cell area) to associate with our custom cell later. 
  * Now bind our new MovieCell class with the custom Cell in IB by selecting Identity Inspector and setting the Class box to "MovieCell" 
  * In the MovieCell.h file, add the following property declarations for the UIImageView, and the two labels in the custom cell. These property declarations automatically create accessor methods that we can later use to write or read from these UI controls. 

`@property (nonatomic, weak) IBOutlet UIImageView *posterView;`
`@property (nonatomic, weak) IBOutlet UILabel *movieTitleLabel;`
`@property (nonatomic, weak) IBOutlet UILabel *synopsisLabel;`

  * The compiler does not know that these IBOutlet properties refer to the UIImageView and UILabels in the custom cell until we connect them doing the following: 
    * In IB, select the cell
    * Click on the Connector Inspector
    * In the Outlets section, you will see the names of the posterView, movieTItleLabel and synopsisLabel IBOUtlets we declared in the MovieCell.h file. The appear hear, because we set the class of this cell to be equal to the MovieCell class we created. Next to the names of these IBOutlets you will see empty circles. Click on them, one by one and drag your mouse to the corresponding UI control in the custom cell to make the connection. The Outlets section will now show that the UI controls are now connected to the IBOutlets. You should now see something like this in the Connector Inspector:
<br/>
<a href="http://imgur.com/WWyIIwr"><img src="http://i.imgur.com/WWyIIwr.png" title="source: imgur.com" /></a>
<br/>

####6.Use RottenTomatoes API to request movies data
We need to retrieve the information about the movies from the RottenTomatoes to load the the MoviesTableView tableView with the cell contents before it is displayed.  
* Declare an NSArray called "movies" in the @interface of MoviewTableViewController. We will use this array to store the JSON data we will get from the RottenTomatoes server.
* In the viewDidLoad method of the MoviesTableViewController add the following code (cut and paste from <script src="https://gist.github.com/mmesarina/a7fe2c1fd77de9f42567.js"></script> (to get data on current movies being played at theaters from the RottenTomatoes server:  

`NSString *url = @"http://api.rottentomatoes.com/api/public/v1.0/lists/movies/in_theaters.json?apikey=ws32mxpd653h5c8zqfvksxw9";` 
<br/>	
`NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:url]];`
<br/>
`[NSURLConnection sendAsynchronousRequest:request queue:[NSOperationQueue mainQueue]` `completionHandler:^(NSURLResponse *response, NSData *data, NSError *connectionError) {`
<br/> 
`id object = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];`		
`self.movies = object[@"movies"];`
`}];`








 