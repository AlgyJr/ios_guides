# Starting a New Project in Xcode

## 1. Create the Project

1. Launch Xcode and choose "Create a new Xcode project" or from within Xcode, Click on File -> New -> Project</br>

   <img src="https://i.imgur.com/7oDWujw.gif" width="300" /></br>

1. Create a "Single View" project, and click *Next*.</br>

   <img src="https://i.imgur.com/Kjr10ht.png" width="400" /></br>

1. You'll be presented with a number of options.
   - **Product Name** - The product name is just the name of your app, e.g., "MyProject"
   - **Organization Name** - This is the name of your company, e.g. "Acme"
   - **Company Identifier** - With the above values, this becomes `com.acme`. By convention, iOS uses [reverse domain name notation](http://en.wikipedia.org/wiki/Reverse_domain_name_notation) for identifiers.
   - **Devices** - This determines if your app is for the iPhone, iPad, or Universal (both). Use Universal.
   - **Language** - Use Swift.
   - **Use Core Data** - It will add the boilerplate Core Data setup code for you. Leave it unchecked.</br>

   <img src="https://i.imgur.com/ssbMW5b.png" width="400" />

1. When you click *Next*, you'll be prompted for a location to save it. Enable "Create a Git respository."</br>

   <img src="https://i.imgur.com/HM7FDKK.png" width="500" /></br>

## 2. Add .gitignore

The .gitignore file will tell git which files to ignore. Here are a few useful .gitignore templates to start with, depending on if you're using Objective-C or Swift.

- [Objective-C .gitignore](https://raw.githubusercontent.com/github/gitignore/master/Objective-C.gitignore)
- [Swift .gitignore](https://raw.githubusercontent.com/github/gitignore/master/Swift.gitignore)
