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
2. Create and run a Podfile to add the  AIFNetworking library

### Movies Screen
3. Add a TableViewController to display list of movies
4. Create custom cell 
5. Use RottenTomatoes API to request movies data
6. Parse data and use it to display title, synopsis and image in cell

### Movie Detail Screen 
7. Add a ViewController to display details of selected movie

### Transition from Movies to Movie Detail screen
8.  Add Segue method to transition 
9.  Create a Movie class to store information to pass to Movie Detail ViewController
10. Display passed movie data in Movie Detail screen


## Steps in detail

### Add AFNetworking library
#### 1.Create an Xcode Project with SingleView application template
* Create the Xcode project:
  * Create an Xcode project using the SingleView template, call it RottenTomatoes 
  * Close the project
#### 2. Create and run a Podfile to add the AIFNetworking library
Cocoapods is a tool to install libraries into our Xcode project. We will use this tool to add the AFNetworking library as part of our Xcode project. If you have not yet installed Cocoapods follow these steps. For detail information on how to install Cocoapods go to http://cocoapods.org/.
*Install Cocoapods
  * Run these gem commands to install Cocoapods: 
    *`gem install cocoapods --user-install`
  * To check where Cocoapods was installed do:
    *`gem which cocoapods`
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
