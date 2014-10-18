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
1. Add a TableViewController to display list of movies
2. Create custom cell 
3. Bind custom cell to source code via a UITableViewCell class 
4. Use RottenTomatoes API to request movies data
5. Parse data and use it to display title, synopsis and image in cell

### Movie Detail Screen 
1. Add a ViewController to display details of selected movie

### Transition from Movies to Movie Detail screen
1.  Add Segue method to transition 
2.  Create a Movie class to store information to pass to Movie Detail ViewController
3. Display passed movie data in Movie Detail screen


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
####1.Add a TableViewController to display list of movies
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

####2.Create Custom Cell
IOS has 4 standard cell types with fixed positions of an image, and two labels. In most cases, this doesn't suffice, so it is recommended to always create your own custom cell, which will show how to do next.
* In IB, select the Table View, select Attribute Inspector and make sure that the Content box is set to "Dynamic Prototypes" as opposed to "Static Cells". This tells Xcode that the apps is not using a fixed number of cells (static) but rather that cells will be dynamically created with the the pattern of the custom cell we are about to create.
* In IB, select the Cell ( it is easier to select the Cell in the Outline View to the left of the canvas), select Attribute inspect and set the "Style" box to "Custom". This tells Xcode that we are creating a custom cell, as opposed to one of the 4 standard ones.
* In addition, in the Attributes Inspector with the Cell selected, set the Identifier box to "MyMovieCell". We will use this identifier later inside our code to dynamically request for IOS to create our custom cell and return a pointer to, so that we can load it with the movie content we want to display.
* From the IB library, drag a UIImageView and two UILabel controls inside the Cell. Place the UIImageView one the left side, one UILabel to the right of the image to be used for the movie title, and one UILabel below to be used for the movie synopsis.
* Make the UILabel used for synopsis at least 6 lines in width by selecting it, going to the Attributes Inspector and setting the Lines box to 6. Also set the Font to a small type size like 12.0. Your canvas with the NavigationController and MoviesTableViewController should look something like this:

<br/>
<a href="http://imgur.com/ICM83lz"><img src="http://i.imgur.com/ICM83lz.png" title="source: imgur.com" /></a>
<br/>

####3.Bind custom cell to source code via a UITableViewCell class
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

####4.Use RottenTomatoes API to request movies data
We need to retrieve the information about the movies from the RottenTomatoes to load the the MoviesTableView tableView with the cell contents before it is displayed.  
* Declare an NSArray called "movies" in the @interface of MoviesTableViewController. We will use this array to store the JSON data we will get from the RottenTomatoes server.
* In the viewDidLoad method of the MoviesTableViewController add the following code ([cut and paste from here](https://gist.github.com/mmesarina/a7fe2c1fd77de9f42567)) to get data on current movies being played at theaters from the RottenTomatoes server:  

`NSString *url = @"http://api.rottentomatoes.com/api/public/v1.0/lists/movies/in_theaters.json?apikey=ws32mxpd653h5c8zqfvksxw9";` 
<br/>	
`NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:url]];`
<br/>
`[NSURLConnection sendAsynchronousRequest:request queue:[NSOperationQueue mainQueue]` `completionHandler:^(NSURLResponse *response, NSData *data, NSError *connectionError) {`
<br/> 
`id object = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];`		
`self.movies = object[@"movies"];`
<br/>
`[self.tableView reloadData];`

`}];`
<br/>
*  The `self.movies = object[@"movies"]` line stores the "movies" value, which is an array,  from the JSON output into our "movies" NSArray. Each element of the JSON array corresponds to data about each movie.
*  The `[self.tableView reloadData]`line, makes sure that the tableView of the MovieTableViewController is reloaded with the new data we just got. The tableView is a property of the MovieTableViewController and represent the actual table. When the "reloadData" method is called on the tableView, three basic TableViewController methods are automatically called to load data into the table. We will talk about these methods, called "data source" methods shortly.

####JSON SIDE NOTE
*If you open a browser and navigate to [http://api.rottentomatoes.com/api/public/v1.0/lists/movies/in_theaters.json?apikey=ws32mxpd653h5c8zqfvksxw9"](http://api.rottentomatoes.com/api/public/v1.0/lists/movies/in_theaters.json?apikey=ws32mxpd653h5c8zqfvksxw9") , which is the endpoint to retrieve data about movies "in theaters", you will get the response in the browser. It will look like this (you need to first install Chrome JSONView extension to be able to see the nice colored format) 

<br/>
<a href="http://imgur.com/qK7HvJo"><img src="http://i.imgur.com/qK7HvJo.png" title="source: imgur.com" /></a>
<br/>
* In JSON:
  * Data is in name/value pairs
  * Data is separated by commas
  * Curly braces hold objects
  * Square brackets hold arrays
Thus looking at the output we can see that 
* There are 143 movies  
* The "movies" element is an array of objects, where each object key/value pairs of data for a movie. Thus an object in JSON is the same as an NSDictionary.  
* For each movie object we will be extracting the "title", "synopsis", "ratings" and "abridged_cast" information. Notice, that "ratings" and "abridge_cast" values are themselves objects (NSDictionaries if you will) and so will require a second level of parsing.



####Data Source Methods for UITableViewControllers Side Note
* Whenever the tableView of a TableViewController instance is sent a message to load itself with data, three main methods, called Data Source methods are triggered. These methods are 
  * numberOfSectionsInTableView - returns the number of sections to display in tableview
  * numberOfRowsInSection - returns the number of rows for a section to display in tableview
  * cellForRowAtIndexPath - returns the cell to display in a particular section and row of the tableview

* These data source methods can be delegated to a different object other than the UITableViewController, but almost always the default choice is to make the UITableViewController the delegate, meaning, the UITableViewController implements them. When using Storyboard like we did, things are hardwired to make the UITableViewController the delegate. There is no need to manually specify that the UITableViewController is the Data Source delegate ( no need to add <UITableViewDelegate,UITableViewDataSource> to *.h, and no need to set the tableview.delegate)
####5.Parse data and use it to display title, synopsis and image in cell
The three data source methods mentioned above are automatically added as boiler plate code when a UITableViewController class instance is created, thus they are already inside the MoviesTableViewController.m file.
* First include the UIImageView+AFNetworking.h that was installed with cocoapods
* Also include the MovieCell.h to be able to load our customCell IBOutlets with content 
* For `numberOfSectionsInTableView` method return 1
* For `numberOfRowsInSection` return `self.movies.count`, which returns the number of movie objects stored in our self.movies array
* For `cellForRowAtIndexPath` method do the following (or cut and paste from [cellRowAtIndexPath method](https://gist.github.com/mmesarina/cbf4c4c9b2cb15d5ca97))
  * Request the tableView to provide a pointer to a new MovieCell instance using the `dequeueReusableCellWithIdentifier` method and the cell identifier @"MyMovieCell" 
  * Retrieve the data that corresponds to the current movie by indexing the "movies" array using the indexpath parameter and storing it in a NSDictionary variable
  * Retrieve the movie's title from the NSDictionary by reading the value with key 'title' and storing it in the cell's title label IBOutlet;
  * Retrieve the movie's synopsis from the NSDictionary by reading the value with the key 'synopsis`and storing in the cell's synopsis label IBOutlet;
  * Retrieve the "posters" value from the NSDictionary by reading the value with the key 'posters' and storing it in another NSDictionary for further drill down. 
  * From the posters NSDictionary retrieve the value with the key "detailed" and store it in a temporary NSString variable. This string represents the URL of the "detailed" image for the movie poster
  * Use the following AFNetworking method to load the "detailed" image into the cell's UIImageView IBOutlet as follows: 
    * `[cell.posterView setImageWithURL:[NSURL URLWithString:posterUrlString]];`
  * Return the custom cell 
  
### Movie Detail Screen
####1.Add a ViewController to display details of selected movie
* In Interface Builder do the following:
  * Drag and drop a ViewController to the Storyboard and call it MovieDetailController
  * Populate the ViewController with an UIImageView control for the movie image, and UILabels for Title, Synopsis,  Cast and Rating
  * Create a transition with a Segue from the cell in the MoviesTableViewController to the MovieDetailController:
    * CTRL-DRAG mouse from the cell to the MovieDetailController
    * As you released the mouse a sub-menu appears, select the "show" entry. This will make a "Segue" appear in the Storyboard connecting the MoviewTableViewController to the MovieDetailController.
    * Select the Segue, go to Attributes Inspector and set the Identifier box to "showDetailView"
* Create a new ViewController class to bind to the MovieDetailController, with class name "MovieDetailController" and sub-class type "ViewController"
* Bind the new class to the MovieDetailController in IB: 
   * select the MovieDetailController, select the Identity Inspector and set the class box to "MovieDetailController"
* Create IBOutlets for the controls in the MovieDetailController.h like so:
  * `@property (weak, nonatomic) IBOutlet UILabel *movieTitle;`
  * `@property (weak, nonatomic) IBOutlet UILabel *synopsis;`
  * `@property (weak, nonatomic) IBOutlet UILabel *cast;`
  * `@property (weak, nonatomic) IBOutlet UILabel *rating;`
* Connect the IBOutlets we just created with the corresponding controls in the MovieDetailController:
  * In IB, select the MovieDetailController
  * Select the Connections Inspector
  * In the Outlets sections you will see the IBOutlet names. With the mouse click on the empty circles next to the names and drag to the appropriate control 

### Transition from Movies to Movie Detail screen
####1.Add Segue method to transition
When the user clicks on a cell in the MoviesTableViewController, the "prepareForSegue:sender" method is called automatically. This method is called to help transition to the a next screen. Within this method one can pass data to the viewController about to be displayed, by storing data in that viewController's properties. The "sender" parameter of this method carries information about the Segue connector that is associated with the current transition. There could be multiple Segues in a design, thus by using the Segue identifier we can identify which Segue is being used. 
<br/> <br/>We will create a new class called "Movie" to store the information about the selected movie that we want to pass to the MovieDetailViewController. To pass this information to the MovieDetailViewController, we need to create a property of this type in its interface and set it in the "prepareForSegue:sender" (in MovieTableViewController) before the "prepareForSegue:sender" method returns. 
* Create a new "Movie" class of type NSObject
* In Movie.h, declare properties to store information about a movie:
  * `@property (nonatomic, strong) NSString *title;`
  * `@property (nonatomic, strong) NSString *synopsis;`
  * `@property (nonatomic, strong) NSString *cast;`
  * `@property (nonatomic, strong) NSString *ratings;`
  * `@property (nonatomic, strong) NSString *imageURL;`
* Declare and define a custom initialization method, "initWithDictionary:dictionary", which expects to be passed a single movie's information as key/value pairs (basically the JSON object corresponding to a single movie)
* Inside the "initWithDictionary:dictionary" method extract the title, synopsis, cast, ratings, and imageURL from the dictionary and store their values in the appropriate Movie instance properties. Here is an example of what this method would do in source code:
<br/>
<a href="http://imgur.com/kNyNvYt"><img src="http://i.imgur.com/kNyNvYt.jpg" title="source: imgur.com" /></a>
<br>



	
	
	
	
 



















 