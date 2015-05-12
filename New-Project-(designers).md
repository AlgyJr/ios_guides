# Creating a new project

## Step 1: Generate Xcode Project

In Xcode, select File->New->Project and choose the Single View Application template, as shown in the figure below. The other templates are just for demo purposes, so you'll always use the Single View Application template.

![New Project|600](http://i.imgur.com/cNAyOcp.gif)

## Step 2: Disable Auto Layout

Layout in iOS is very simple, it's based on absolute positioning and nesting views. Unlike HTML, there is no flow (automatic wrapping of views) and no box model (margin and padding). Auto Layout is a set of rules that allows the views to be elastic to small dimension changes and responsive to orientation changes and to phones / tablets.

Until we learn how to use Auto Layout, it can complicate things unnecessarily, so disable it for new projects by selecting the Storyboard and unchecking Auto Layout, as shown below.

![Disable Auto Layout|600](http://i.imgur.com/QTqgV9N.gif)
