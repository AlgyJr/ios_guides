## Overview

Table Views are one of the most common root views on iOS. They are used for displaying long lists of data, such as:

* A mail app's inbox view
* A settings screen
* A list of movies with their poster images

Table View can be highly performant, displaying thousands of rows (*cells*) of data. They also have common behavior baked in, such as and "editing mode", and the ability to animate the addition or removal of rows.

This guide will cover the fundamentals of using a tableviews.

## How Tableviews Work

If you had to display a list of a thousand emails in your inbox, your first thought may be to write a for-loop that adds a thousand views on screen. This would require a lot of memory, and take a long time to fill in the data in each views.

A tableview fakes large lists of data by only loading up each row when needed. While you may have a thousand rows of data, if only seven are visible on screen at once, then the app will only need seven rows. As you scroll, a new cell created on-demand.

To make things even faster, as old cells are *recycled*. As a cell scrolls off screen, it is placed in a reuse-queue; think of it as a recycling bin. Rather than have to initialize a new cell from scratch, you grab one of the old cells from the bin, and reconfigure its views to represent the new data.

## Basic Implementation

As with Cocoa, rather than subclassing `UITableView`, you use the delegate pattern. Your view controller implements a few methods, and then assign the views controller to the Table View's `dataSource` property.

The simplest example is where all of your cells have the same height, such as your mail app's Inbox view. The tableview only needs to know three things: the cell height, the number of rows, and how to create each cell on-demand.

If you say there are a ten rows, and each row's height is 100. The tableview will know table's total height is 1,000, and the scrollbar will look correct. Then, when the tableview needs the cell, it calls the cell generator, supplying the row number.

### In Code

Say we have an array of `Movie` objects we want to display in the table view. For illustrative purposes, we won't worry about recycling cells just yet.

First, add the `UITableViewDataSource` protocol to your class definition, or you'll get warnings.

```
class MoviesViewController: UIViewController, UITableViewDataSource
```

Now configure the Table View. You can do it in Interface Builder or with code.

```
override func viewDidLoad(){
  moviesTableView.rowHeight = 100.00
  moviesTableView.dataSource = self
}
```

Now supply the number of rows:

```
func tableView(tableView: UITableView!, numberOfRowsInSection section: Int) -> Int {
  return movieArray.count
}
```

Now supply the cell factory method:

```
func tableView(tableView: UITableView!, cellForRowAtIndexPath indexPath: NSIndexPath!) -> UITableViewCell! {
  let movie = movieArray[indexPath.row]
  let cell = UITableViewCell(style: UITableViewCellStyle.Default, reuseIdentifier: "")
  cell.textLabel.text = movie.title as NSString
  return cell
}
```

### Cell Reuse

As cells get more complex in design, they can be more expensive to create on the fly. This can cause jerky "pops" when a new cell appears. You should probably reuse cells.

First, register a reuse-queue. We only use one cell type in our table view, so it's straightforward.

```
override func viewDidLoad(){
  moviesTableView.rowHeight = 100.00
  moviesTableView.dataSource = self
  movieTableView.registerClass(UITableViewCell.self, forCellReuseIdentifier: "com.codepath.moviesapp.movieCell")
}
```

You must give it a unique identifier because a single table view could have several cells classes. For instance, Facebook's News Feed is a mix of photos, web links, and status updates. Each probably have their own UITableViewCell subclass.

Next, instead of creating a new cell, we ask for one from the tableview. It will either provide an existing one, or create a new one if necessary.

```
func tableView(tableView: UITableView!, cellForRowAtIndexPath indexPath: NSIndexPath!) -> UITableViewCell! {
  let movie = movieArray[indexPath.row]
  let cell = tableView.dequeueReusableCellWithIdentifier("com.codepath.moviesapp.movieCell") as UITableViewCell
  cell.textLabel.text = movie.title as NSString
  return cell
}
```

## Variable Height Cells

There are three ways to work with dynamic height, and each have tradeoffs around performance or complexity.

### Explicit Height

You can provide a method that will be called once for each cell when your tableview first loads. In this method, you return the height.

Say we want the row height to match the size of our movie poster's thumbnail image.

```
func tableView(tableView: UITableView!, heightForRowAtIndexPath indexPath: NSIndexPath!) -> CGFloat {
  let movie = movieArray[indexPath.row]
  return movie.moviePosterImage.thumbnailSize.height
}
```

### Using Estimated Height

If you have 1,000 rows, first time you load the tableview, `tableView(heightForRowAtIndexPath:)` will be called 1,000 times. It must be performant or the UI will hang on load.

If there's no way to make it fast, you can provide an *estimated height*. This value will be used to calculate the full table view height. `tableView(heightForRowAtIndexPath:)` is only called with the first time the cell scrolls on screen.

If your estimated view height is around 100:

```
movieTableView.estimatedRowHeight = 100
```

This is not without its drawbacks. There are [reports of bugs](http://blog.jaredsinclair.com/post/81410534498/cell-height-caching-dilemmas-in-unread) as of iOS 7.

### Using Auto Layout

It can be more productive, and less error prone, to use [[Auto Layout|Auto Layout]]. Just setup your cells with constraints, and then set:

```
movieTableView.rowHeight = UITableViewAutomaticDimension
```

## Responding to Cell Taps

You probably want to respond in some way when a user taps on a cell, like pushing into a detailed view. To do this, you implement a `UITableViewDelegate` method, `tableView(didSelectRowAtIndexPath:)`

First, update the class definitions:

```
class MoviesViewController: UIViewController, UITableViewDataSource, UITableViewDelegate
```

Update your `viewDidLoad()` to assign the delegate:

```
moviesTableView.delegate = self
```

Then implement the delegate method:

```
func tableView(tableView: UITableView!, didSelectRowAtIndexPath indexPath: NSIndexPath!) {
  // Perform your action
}
```