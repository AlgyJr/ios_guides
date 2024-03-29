Git is an essential tool in the developers toolkit. Git stores your files like a stream of snapshots, allowing you and other collaborators to make changes to projects and keep everything in sync. This Guide introduces using Git with Terminal. You can click the video link in each section for a video walkthrough or [watch the video playlist now](https://www.youtube.com/playlist?list=PLrT2tZ9JRrf4KDwVwtpszLUxf_CuL2l4y).

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

![Drag Folder into Terminal|400](https://i.imgur.com/5V7YtYJ.gif)

### Creating a Local Git Repository
  
<a href="http://www.youtube.com/watch?feature=player_embedded&v=Fxe8BW8QWa0
" target="_blank"><img src="http://img.youtube.com/vi/Fxe8BW8QWa0/0.jpg" 
alt="Loading" width="240" height="180" border="10" /></a>  
 
Local Git repositories are created and managed locally on your computer. 
- Xcode will automatically create a local Git repository for your project if you select, "Create Git repository on... My Mac", when you first create your project. You should **Always** select this when creating a new Xcode project.  
  
    ![Creating a Local Git Repository Xcode gif|700](https://i.imgur.com/aJhndKZ.png)  

If you already have a project, where a Git repository was not created when you made the project, you can create a local Git repository using terminal. 
- Navigate to your Xcode Project folder in Terminal.

    ```bash
    git init
    ```

### Creating a .gitignore File
  
<a href="http://www.youtube.com/watch?feature=player_embedded&v=N6wQ28h-MmE
" target="_blank"><img src="http://img.youtube.com/vi/N6wQ28h-MmE/0.jpg" 
alt="Loading" width="240" height="180" border="10" /></a>  
   
When collaborating using Git, you will inevitably run into "merge conflicts". However, you will save yourself from a lot of extraneous conflicts by adding a `.gitignore` file with the proper content.
- Navigate to your Xcode Project folder in Terminal.
- Use `ls -a` to list all files including hidden files to check if a `.gitignore` has already been created.
- If there is not an existing `.gitignore` file, create one.

    ```bash
    touch .gitignore
    ``` 

### Add Files and Directories to .gitignore
- Navigate to your Xcode Project folder in Terminal.
- Open your `.gitignore` file using, `open .gitignore`  
- Copy and paste the [latest and greatest list of files and folders you want to ignore](https://raw.githubusercontent.com/github/gitignore/master/Swift.gitignore) into the .gitignore file.

### Create and Configure .gitattributes File
  
<a href="http://www.youtube.com/watch?feature=player_embedded&v=MNYxq3eoCN4
" target="_blank"><img src="http://img.youtube.com/vi/MNYxq3eoCN4/0.jpg" 
alt="Loading" width="240" height="180" border="10" /></a>  
  
This file allows us to tell Git how we want certain files to be treated. We will add a file to the `.gitattributes` that tells Git to treat any file with `.pbxproj` as binary.
- Navigate to your Xcode Project folder in Terminal.
- Use `ls -a` to list all files including hidden files to check if a `.gitattributes` has already been created.
- If there is not an existing `.gitattributes file, create one. 

    ```bash
    touch .gitattributes
    ```

- Open your `.gitattributes` file, `open .gitattributes`
- Add the following code to the `.gitattributes` file.

    ```bash
    *.pbxproj binary merge=union
    ```
### Add and configure README

- Add a README.md file to your repo
    
    ```bash
    touch README.md
    ```
- Open and edit your README.md

    ```bash
    open README.md
    ```

### Adding and Committing Changes to Local Repository
  
<a href="http://www.youtube.com/watch?feature=player_embedded&v=G9l7YJ6K0zc
" target="_blank"><img src="http://img.youtube.com/vi/G9l7YJ6K0zc/0.jpg" 
alt="Loading" width="240" height="180" border="10" /></a>  
  
Now that your local Git is all setup from the last step, you can update your local Git repository with any changes you make to your project.
- Navigate to your Xcode Project folder in Terminal.
- Check for any changes that have been made to files since your last commit

    ```bash
    git status
    ```

- Stage any files with changes you'd like to commit.

    ```bash
    git add [filepath] # This adds an individual file
    git add . # This adds all files with changes
    ``` 

- Check to see what was added.

    ```bash
    git status
    ```

- Apply the changes to your local Git repository with a message briefly outlining the changes you made.

    ```bash
    git commit -m "Here is my commit message"
    ```

### Linking to a Remote Repository
  
<a href="http://www.youtube.com/watch?feature=player_embedded&v=8JVmXZ_xreQ
" target="_blank"><img src="http://img.youtube.com/vi/8JVmXZ_xreQ/0.jpg" 
alt="Loading" width="240" height="180" border="10" /></a>  
  
There are many remote repository options. In this guide we will be using [GitHub](https://github.com/). You will need a GitHub account if you don't have one already.

#### Repository URLs

There are two types of repository URLs, [HTTPS and SSH](https://help.github.com/articles/which-remote-url-should-i-use/):

- HTTPS: `https://github.com/homer/duff_project.git`
- SSH: `git@github.com:homer/duff_project.git`

The general workflow is the same for both URLs, but there are some differences in the specifics of commands.

#### SSH Setup
You'll first need to [register your machine's SSH keys](https://help.github.com/articles/generating-an-ssh-key/) with your Github account. You only need to perform this step **once** for each machine you use.

Once you've added your SSH key, the SSH repository URL will look like `git@github.com:myusername/reponame.git` but for your username and project. You won't need to provide credentials (username/password) for any further git commands.

#### HTTPS Setup

There is no additional setup required for using HTTPS. However, you'll need to enter your username and password each time you run the `git pull` or `git push` commands:
```
git push origin master
<enter your username at promt>
<enter your password at prompt>
```

#### Create a new Repository on GitHub  
    
   <img src="https://i.imgur.com/UmHxuhN.gif" width="600" />

- Navigate to your Xcode Project folder in Terminal.  
- Link to the remote repository by adding the following code in Terminal, provided from your GitHub repository. We want to "push an existing repository from the command line".
   - NOTE: Make sure you add YOUR GitHub remote address! 
  
    ![Link to the remote repository gif|800](https://i.imgur.com/48uur3D.gif)  

   - NOTE: Decide if you want the remote to use [SSH or HTTPS](http://guides.codepath.com/ios/Using-Git-with-Terminal#ssh-setup) before setting your remote in the next step. I like ssh for the convenience of not always having to input my credentials.  

    ```bash
    git remote add origin https://github.com/yourUserName/yourRepoName.git
    git push -u origin master
    ```

### Syncing Local Repository With Your Remote Repository
When you have made changes to your project and used the `git add .`, `git status` and `git commit -m "Message"` commands to update your local Git repository, the next step is to sync with your remote repository. Luckily, keeping in sync can be done with only two additional steps, `git push` and `git pull`.  
  
- Navigate to your Xcode Project folder in Terminal.
- Push changes you have made locally to update the remote repository.

    ```bash
    git push
    ```

- Pull changes that have been added to the remote repository by a collaborator to update your local repository.

    ```bash
    git pull
    ```

### Cloning a Remote Repository
  
<a href="http://www.youtube.com/watch?feature=player_embedded&v=2-nFvX6vEoI
" target="_blank"><img src="http://img.youtube.com/vi/2-nFvX6vEoI/0.jpg" 
alt="Loading" width="240" height="180" border="10" /></a>
  
If you are collaborating on a project, or just want access to your project from a different computer, you will need to **clone** from the remote repository.
- Get the url of the remote repository. You can access this by going to the remote repository in GitHub and copying the clone link, bottom right.  
  
![Get the url of the remote repository](https://i.imgur.com/XWEyeAo.png)  
  
- In Terminal, navigate to the directory you want to clone the repository in.
- Clone the repository...

    ```bash
    git clone https://yourRemoteRepositoryUrl
    ```

### Dealing With Merge Conflicts
  
<a href="http://www.youtube.com/watch?feature=player_embedded&v=lo8oLnY7H_4
" target="_blank"><img src="http://img.youtube.com/vi/lo8oLnY7H_4/0.jpg" 
alt="Loading" width="240" height="180" border="10" /></a> 
  
Inevitably, there will come a time when you AND a collaborator will make changes to the same file and both try to **push** to the remote repository. This will result in a **Merge Conflict**.
- In the instance of a Merge Conflict, you will probably get an error in the terminal when attempting to **push** your local changes.  

   <img src="https://i.imgur.com/yWEhJdu.png" width="600" />
  
- As, the error message suggests in the "Hint", the next step is to perform a `git pull`. The pull will likely result in a message similar to the following. 
   - NOTE: The error message confirms we have a Merge Conflict in the Main.storyboard file and tells us that we need to fix the conflict and commit the changes.   
  
   <img src="https://i.imgur.com/pqoEIP3.png" width="600" />

- Use `git status` to confirm which file(s) are causing the conflict.  
  
   <img src="https://i.imgur.com/gFBaNZt.png" width="600" />
  
- Use `open /yourfileHere/` to open up the file in conflict; this will open up the file in Xcode. 
   - NOTE: In this particular example, the conflict is in the Main.storyboard file. We are usually looking at the Interface Builder view of this file, so to reveal the underlying code, right-click on the `Main.storyboard` file and choose, **Open As -> Source Code**.  
  
   ![open conflict file|800](https://i.imgur.com/kVu9aIn.gif)  
  
- Identify the conflicting lines of code; they will be surrounded by, `<<<<<<< HEAD     >>>>>>>bunchOfNumbersAndLetters` and separated by `=======`. 

- Choose one to keep and one to delete. Do your best to figure out what the differences are; in this example, we see that the initialViewController is set to a different ID in each, `"BYZ-38-t0r"` vs. `"NFr-Eg-PaI"`.  
  
   ![](https://i.imgur.com/ZDIbTtk.gif)  

- Clean your Project, cmd + shift + K
   - If your resolved conflict was in the Main.storyboard file, right + click on the storyboard file and choose **Open As -> Interface Builder - Storyboard**.

- Finally, **push** your local changes to the remote repository.

  
