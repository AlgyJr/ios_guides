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
### Add AIFNetworking library 
1. Create an Xcode Project with SingleView application template
2. Create and run a Podfile to add the  AIFNetworking library
### Movies Screen
3. Add a TableViewController to list movies
4. Create custom cell 
5. Use RottenTomatoes API to request movies data
6. Parse data and use it to display title, synopsis and image in cell
### Movie Detail Screen 




