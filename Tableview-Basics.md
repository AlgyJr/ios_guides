## Overview

Table Views are one of the most common root views on iOS. They are used for displaying long lists of data, such as:

* A mail app's inbox view
* A settings screen
* A list of albums with covers

Table View are highly performant, and can display thousands of rows (*cells*) of data without a problem. They also have common behavior baked in, such as and "editing mode", and the ability to animate the addition or removal of rows.

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
}
```

## Cell Reuse

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
}
```
