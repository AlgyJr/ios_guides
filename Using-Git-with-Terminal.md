Git is an essential tool in the developers toolkit. Git stores your files like a stream of snapshots, allowing you and other collaborators to make changes to projects and keep everything in sync. This Guide will cover using Git with Terminal.

### Basic Terminal Navigation
The Terminal is much like the finder, but less pretty. There are just a few basic commands needed to navigate around in Terminal. Using Terminal, we often refer to folders as directories. Check out this [Terminal Cheat Sheet for Mac](https://github.com/0nn0/terminal-mac-cheatsheet/wiki/Terminal-Cheatsheet-for-Mac-(-basics-)) for a more complete list of commands.
- Open up terminal, cmd + space "terminal".
- `pwd` Figure out which directory you are currently in by "Printing the Working Directory".
- `ls` List the directories and files inside the current directory.
   - `ls -a` include hidden files in the list of directories and files. This is helpful when trying to find hidden files like `.git` or `.gitignore` 
- `cd [folder]` Go into the folder. e.g. `cd Desktop/Developer`
   - `cd` This by itself will navigate all the way out to your **Home Directory** 
   - `cd ..` Back out to the parent directory of the current directory.
- `open [file]` Same as "double-clicking" a file or folder in finder.
   - `open .` Opens the current folder or file in finder or it's default application. 
- `clear` When you need a fresh Terminal window  
  
**Hint** If you can't find the path to a file or folder through terminal, search for it using Finder, then drag the file or folder right into your terminal window! If you are trying to change to that directory, you will need to type `cd` before dragging in the folder.  

![Drag Folder into Terminal|400](http://i.imgur.com/5V7YtYJ.gif)

### Creating a Local Git Repository
Local Git repositories are created and managed locally on your computer. 
- Xcode will automatically create a local Git repository for your project if you select, "Create Git repository on... My Mac", when you first create your project. You should **Always** select this when creating a new Xcode project.  
  
![Creating a Local Git Repository Xcode gif|700](http://i.imgur.com/aJhndKZ.png)  

If you already have a project, where a Git repository was not created when you made the project, you can create a local Git repository using terminal. 
- Navigate to yor Xcode Project folder in Terminal.

```
git init
```

### Creating a .gitignore File
When collaborating using Git, you will inevitably run into "merge conflicts". However, you will save yourself from a lot of extraneous conflicts by adding a `.gitignore` file with the proper content.
- Navigate to your Xcode Project folder in Terminal.
- Use `ls -a` to list all files including hidden files to check if a `.gitignore` has already been created.
- If there is not an existing `.gitignore` file, create one.

```
touch .gitignore
``` 

### Add Files and Directories to .gitignore
- Navigate to your Xcode Project folder in Terminal.
- Open your `.gitignore` file using, `open .gitignore`  
- Copy and paste the [latest and greatest list of files and folders you want to ignore](https://raw.githubusercontent.com/github/gitignore/master/Swift.gitignore) into the .gitignore file.
   - NOTE: Be sure to "un-comment" the `#Pods/` by deleting the `#` so you have, `Pods/`  
!["un-comment" the #Pods/ gif|800](http://i.imgur.com/JtOsEw2.gif)  
  
### Adding and Committing Changes to Local Repository
Now that your local Git is all setup from the last step, you can update your local Git repository with any changes you make to your project.
- Navigate to your Xcode Project folder in Terminal.
- Stage all the changes you made.

```
git add .
``` 

- Check to see what was added.

```
git status
```

- Apply the changes to your local Git repository with a message briefly outlining the changes you made.

```
git commit -m "Here is my commit message"
```

### Linking to a Remote Repository
There are many remote repository options. In this guide we will be using [GitHub](https://github.com/). You will need a GitHub account if you don't have one already.
- Create a new Repository on GitHub  
![Create a new Repository on GitHub gif|800](http://i.imgur.com/UmHxuhN.gif)  

- Navigate to your Xcode Project folder in Terminal.  
- Link to the remote repository by adding the following code in Terminal, provided from your GitHub repository. We want to "push an existing repository from the command line".
   - NOTE: Make sure you add YOUR GitHub remote address! 
  
![Link to the remote repository gif|800](http://i.imgur.com/48uur3D.gif)  

```
git remote add origin https://github.com/yourUserName/yourRepoName.git
git push -u origin master
```

### Syncing Local Repository With Your Remote Repository
When you have made changes to your project and used the `git add .`, `git status` and `git commit -m "Message"` commands to update your local Git repository, the next step is to sync with your remote repository. Luckily, keeping in sync can be done with only two additional steps, `git push` and `git pull`.  
  
- Navigate to your Xcode Project folder in Terminal.
- Push changes you have made locally to update the remote repository.

```
git push
```

- Pull changes that have been added to the remote repository by a collaborator to update your local repository.

```
git pull
```

### Dealing With Merge Conflicts
Inevitably, there will come a time when you AND a collaborator will make changes to the same file and both try to **push** to the remote repository. This will result in a **Merge Conflict**.
- In the instance of a Merge Conflict, you will probably get an error in the terminal when attempting to **push** your local changes.  

![Merge Conflict error](http://i.imgur.com/MKlXmot.png)  
  
- As, the error message suggests in the "Hint", the next step is to perform a `git pull`. The pull will likely result in a message similar to the following...  
  
![](http://i.imgur.com/ASA7nDE.png)

- Use `git status` to find out the which file(s) are causing the conflict  
  
![](http://i.imgur.com/KtjHswK.png)  
  
- Use `open /file/` to open up the file in conflict; this will open up the file in Xcode. 
   - NOTE: In this particular example, the conflict is in the Main.storyboard file. We are usually looking at the Interface Builder view of this file, so to reveal the underlying code, right-click on the `Main.storyboard` file and choose, **Open As -> Source Code**.  
  
![open conflict file|800](http://i.imgur.com/kVu9aIn.gif)  
  
- Identify the conflicting lines of code; they will be surrounded by, `<<<<<<< HEAD     >>>>>>>bunchOfNumbersAndLetters` and separated by `=======`. 

- Choose one to keep and one to delete. Do your best to figure out what the differences are; in this example, we see that the initialViewController is set to a different ID in each, `"BYZ-38-t0r"` vs. `"NFr-Eg-PaI"`.  
  
![](http://i.imgur.com/ZDIbTtk.gif)  

- Clean your Project, cmd + shift + K
   - If your resolved conflict was in the Main.storyboard file, right + click on the storyboard file and choose **Open As -> Interface Builder - Storyboard**.

- Finally, **push** your local changes to the remote repository.

  