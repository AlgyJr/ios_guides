## Overview
Providing a way for users to search through a collection of items is a
fairly common task in iOS projects.  A standard interface for
implementing search behaviors is the [`UISearchBar`][searchbar].

[searchbar]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISearchBar.html#//apple_ref/doc/uid/TP40012857-UISearchBar-SW1

There are a few common ways to work with Search Bars:

* **Directly using a [`UISearchBar`][uisearchbar]**.  This is the most
  bare bones way to use `UISearchBars`.  This can be extremely flexible if
you want to design and program your own search interface, however does
not provide as many built-in features as the other methods.

* **Using a [`UISearchDisplayController`][uisearchdisplaycontroller] to
  help manage a search interface.**  The `UISearchDisplayController` a
allows you to present a standard search interface with built-in
animations.  This method forces you to display search results in a table
view.

* **Using a [`UISearchController`][uisearchcontroller] to help manage a
  search interface.**  The `UISearchController`  is a newer controller
(available only in iOS 8+) that helps you present a search interface
using any kind of view to display the search results.

[uisearchbar]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISearchBar_Class/
[uisearchdisplaycontroller]: https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/
[uisearchcontroller]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISearchController/

This guide covers the very basics of working with each of these classes.
None of these classes actually implements the "searching" behavior of
finding items that match a given query string, since determining which
objects match will vary with the domain specific use case (e.g. when
searching for "people" you might want to match on just their names,
whereas you may want a full-text pre-indexed search when searching
through e-mails).  You'll have to implement any search/filtering
behavior yourself.

## Working with UISearchBars directly
At its core, a search bar is nothing more than a glorified text field
packaged with a [scope]() control and some animations and a couple of
buttons.  Each search bar has a delegate that gives you an opportunity
to respond to user actions.  The most important delegate methods are:

* [`textDidChange`][textdidchange] - most of the time you'll respond to
  this event by updating the displayed set of search results as the user
is typing out a query
* [`searchBarSearchButtonClicked`][searchbuttonclicked] - in some cases
if the search operation is slow (e.g. requires making a slow API call)
you'll want to wait until the user taps the search button before
updating the search results.
* [`selectedScopeButtonIndexDidChange`][scopechanged] - you'll want to
  update the search results if the user changes search scopes

[textdidchange]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISearchBarDelegate_Protocol/index.html#//apple_ref/occ/intfm/UISearchBarDelegate/searchBar:textDidChange:
[searchbuttonclicked]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISearchBarDelegate_Protocol/index.html#//apple_ref/occ/intfm/UISearchBarDelegate/searchBarSearchButtonClicked:
[scopechanged]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISearchBarDelegate_Protocol/index.html#//apple_ref/occ/intfm/UISearchBarDelegate/searchBar:selectedScopeButtonIndexDidChange:

### Example searching a table
We start out with a single view application with [a basic
UITableView](Table-View-Guide#reusing-uitableviewcells).  You can add a
`UISearchBar` as you would with any other control by dragging one to
your view controller in interface builder or by programmatically adding
it.

<a href="http://imgur.com/TbVI3Yv"><img src="http://i.imgur.com/TbVI3Yv.gif" title="source: imgur.com" /></a>

The code to implement the search behavior is as follows.  We maintain an
additional array `filteredData` to represent rows of data that match our
search text.  When the search text changes we update `filteredData` and
reload our table.

```swift
class ViewController: UIViewController, UITableViewDataSource, UISearchBarDelegate {
    @IBOutlet weak var tableView: UITableView!
    @IBOutlet weak var searchBar: UISearchBar!

    let data = ["New York, NY", "Los Angeles, CA", "Chicago, IL", "Houston, TX",
        "Philadelphia, PA", "Phoenix, AZ", "San Diego, CA", "San Antonio, TX",
        "Dallas, TX", "Detroit, MI", "San Jose, CA", "Indianapolis, IN",
        "Jacksonville, FL", "San Francisco, CA", "Columbus, OH", "Austin, TX",
        "Memphis, TN", "Baltimore, MD", "Charlotte, ND", "Fort Worth, TX"]

    var filteredData: [String]!

    override func viewDidLoad() {
        super.viewDidLoad()
        tableView.dataSource = self
        searchBar.delegate = self
        filteredData = data
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCellWithIdentifier("TableCell") as UITableViewCell
        cell.textLabel?.text = filteredData[indexPath.row]
        return cell
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return filteredData.count
    }

    func searchBar(searchBar: UISearchBar, textDidChange searchText: String) {
        filteredData = searchText.isEmpty ? data : data.filter({(dataString: String) -> Bool in
            return dataString.rangeOfString(searchText, options: .CaseInsensitiveSearch) != nil
        })

        tableView.reloadData()
    }
}
```

Here's what this looks like when running.  Notice that the search
results are displayed in the same table, and there is presentation of a
separate search interface.

<a href="http://imgur.com/xDqRQEJ"><img src="http://i.imgur.com/xDqRQEJ.gif" title="source: imgur.com" /></a>

### Example searching a collection view

Since the `UISearchBar` is quite simple, it can be combined with any
abitrary view to build your own search interface.  Here's what it might
look like paired with a collection view.

<a href="http://imgur.com/PKY6m7O"><img src="http://i.imgur.com/PKY6m7Ol.gif" title="source: imgur.com" /></a>

The code for this is essentially the same as in the case with table views.

## Using UISearchDisplayControllers
_NB: The `UISearchDisplayController` is deprecated in iOS 8.0.  You may
wish to use the `UISearchController` instead if you are targetting
exclusively iOS 8 and above._

A `UISearchDisplayController` provides more built-in logic for
presenting a search interface&mdash;this is a specific interface for
searching that overlays on top of the current view controller when the
user taps on the search bar.  As part of the search interface **a
`UISearchDisplayController` will display the search results in its own
table view**.  As a result there are four delegates that are involved
when working with a UISearchDisplayController

* the search display controller's
  [`delegate`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/#//apple_ref/occ/instp/UISearchDisplayController/delegate)
which is can respond to the search interface being presented/dismissed
and to changes in search text/scope
* the [`searchResultsDataSource`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/#//apple_ref/occ/instp/UISearchDisplayController/searchResultsDataSource) for the search results table view
* the [`searchResultsDelegate`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/#//apple_ref/occ/instp/UISearchDisplayController/searchResultsDelegate) for the search results table view
* the associated search bar's delegate which will let you respond to
  lower level events on the search bar itself

### Example searching a table

To use of a search display controller you can drag the `Search Bar and
Search Display Controller` item from the Object Library into your view
controller.  This will automatically set the `delegate`,
`searchResultsDataSource`, and `searchResultsDelegate` to this view
controller.  This is typical, but you may want to change these bindings
depending on your use case.  Note that this also adds a `UISearchBar` to
the interface and sets the search display controller's search bar outlet
to it.

<a href="http://imgur.com/Ud8Rvi1"><img src="http://i.imgur.com/Ud8Rvi1.gif" title="source: imgur.com" /></a>

You can also instantiate a search display controller programatically by
[specifying a search bar and containing view
controller](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/#//apple_ref/occ/instm/UISearchDisplayController/initWithSearchBar:contentsController:).
When creating a search display controller programatically, you'll have
to set all the delegates manually.

In either case (instatiation via Interface Builder or programatically),
the containing view controller will have its
[`searchDisplayController`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instp/UIViewController/searchDisplayController)
property set to newly created search display controller.

Here's what the code to implement the search behavior might look like.
In this case (and typically) we take advantage of the fact that our
`ViewController` already implements `UITableViewDataSource` and thus the
same methods can be used to return the appropriate rows in the search
results.  Notice however that the search display controller's
[`searchResultsTableView`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/#//apple_ref/occ/instp/UISearchDisplayController/searchResultsTableView)
and our main table view are two different tables.  We check which table
is calling our `UITableViewDataSource` methods and return the
appropriate data (either the base data set or the filtered search
results).

Finally we update our `filteredData` in the delegate method
[`shouldReloadTableForSearchString`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayDelegate_Protocol/index.html#//apple_ref/occ/intfm/UISearchDisplayDelegate/searchDisplayController:shouldReloadTableForSearchString:)

```swift
class ViewController: UIViewController, UITableViewDataSource, UISearchDisplayDelegate {
    @IBOutlet weak var mainTableView: UITableView!

    let data = ["New York, NY", "Los Angeles, CA", "Chicago, IL", "Houston, TX",
        "Philadelphia, PA", "Phoenix, AZ", "San Diego, CA", "San Antonio, TX",
        "Dallas, TX", "Detroit, MI", "San Jose, CA", "Indianapolis, IN",
        "Jacksonville, FL", "San Francisco, CA", "Columbus, OH", "Austin, TX",
        "Memphis, TN", "Baltimore, MD", "Charlotte, ND", "Fort Worth, TX"]

    var filteredData: [String]!

    override func viewDidLoad() {
        super.viewDidLoad()
        mainTableView.dataSource = self
        filteredData = data
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        // we dequeue from the mainTableView since the one passed in might be the
        // tableView for the searchDipslayController with which we never registered
        // a reusable protoype cell
        let cell = self.mainTableView.dequeueReusableCellWithIdentifier("TableCell") as UITableViewCell

        if tableView == searchDisplayController?.searchResultsTableView {
            //search results table
            cell.textLabel?.text = filteredData[indexPath.row]
        } else {
            // main table
            cell.textLabel?.text = data[indexPath.row]
        }

        return cell
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        if tableView == searchDisplayController?.searchResultsTableView {
            //search results table
            return filteredData.count
        }
        // main table
        return data.count
    }

    func searchDisplayController(controller: UISearchDisplayController, shouldReloadTableForSearchString searchString: String!) -> Bool {
        filteredData = searchString.isEmpty ? data : data.filter({(dataString: String) -> Bool in
            return dataString.rangeOfString(searchString, options: .CaseInsensitiveSearch) != nil
        })
        return true
    }
}
```

Here's what this looks like when running.  Notice that after tapping on
the search bar, the search display controller does an animation to
present the "search interface".  Also notice that the search display
controller dims the current table view before presenting its own search
results table.

<a href="http://imgur.com/oL57lAj"><img src="http://i.imgur.com/oL57lAj.gif" title="source: imgur.com" /></a>

### Example searching a collection view
A key drawback of using the search display controller is that it forces
your search results to be in table view.  For example, we cannot
implement the inline search of a collection view as above without
subclassing `UISearchDisplayController` and a lot of extra work.
Instead here is what the default behavior looks like.

<a href="http://imgur.com/oslCSN6"><img src="http://i.imgur.com/oslCSN6.gif" title="source: imgur.com" /></a>

In the code for this our `ViewController` now has to manage both the
contents of the collection view and the results table view.

```swift
class ViewController: UIViewController, UICollectionViewDataSource, UISearchBarDelegate {
    @IBOutlet weak var collectionView: UICollectionView!

    let data = ["New York, NY", "Los Angeles, CA", "Chicago, IL", "Houston, TX",
        "Philadelphia, PA", "Phoenix, AZ", "San Diego, CA", "San Antonio, TX",
        "Dallas, TX", "Detroit, MI", "San Jose, CA", "Indianapolis, IN",
        "Jacksonville, FL", "San Francisco, CA", "Columbus, OH", "Austin, TX",
        "Memphis, TN", "Baltimore, MD", "Charlotte, ND", "Fort Worth, TX"]

    var filteredData: [String]!

    override func viewDidLoad() {
        super.viewDidLoad()
        collectionView.dataSource = self
        filteredData = data
        searchDisplayController?.searchResultsTableView.registerClass(UITableViewCell.self, forCellReuseIdentifier: "TableCell")
    }

    func collectionView(collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return data.count
    }

    func collectionView(collectionView: UICollectionView, cellForItemAtIndexPath indexPath: NSIndexPath) -> UICollectionViewCell {
        let cell = collectionView.dequeueReusableCellWithReuseIdentifier("TextCell", forIndexPath: indexPath) as TextCell
        cell.textLabel.text = data[indexPath.row]
        return cell
    }
    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {

        let cell = tableView.dequeueReusableCellWithIdentifier("TableCell") as UITableViewCell
        cell.textLabel?.text = filteredData[indexPath.row]
        return cell
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return filteredData.count
    }

    func searchDisplayController(controller: UISearchDisplayController, shouldReloadTableForSearchString searchString: String!) -> Bool {
        filteredData = searchString.isEmpty ? data : data.filter({(dataString: String) -> Bool in
            return dataString.rangeOfString(searchString, options: .CaseInsensitiveSearch) != nil
        })
        return true
    }
}

```

## Using UISearchControllers (iOS 8+)
A newer way to manage the presentation of a search interface (only
available in iOS 8 and above) is via the `UISearchController`.  This
controller handles some of the logic and animation of presenting a
separate search interface for you while still allowing you to specify
how your search results are displayed.

### Example searching a table

There is currently no built-in object in the Interface Builder Object
Library for a `UISearchController`.  The easiest way to create one is to
do it programatically.  This also creates a `UISearchBar` and sets the
search controller's
[`searchBar`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISearchController/#//apple_ref/occ/instp/UISearchController/searchBar)
property to it.  You can add this search bar to your view hierarchy
programatically.

In order to update your search results you'll have to implement the
[`UISearchResultsUpdating`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISearchResultsUpdating_ClassRef/)
protocol and set the search controller's [`searchResultsUpdater`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISearchController/#//apple_ref/occ/instp/UISearchController/searchResultsUpdater) property.

You don't need to implement the
[`UISearchControllerDelegate`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISearchControllerDelegate_Ref/index.html#//apple_ref/swift/intf/UISearchControllerDelegate)
unless you need to hook into the events around the presentation of the
search interface.

Putting it all together the code looks like this.  Notice that we have
to read the search text from the search bar in
`updateSearchResultsForSearchController`.  One other thing to note is
that we set this view controller's `definesPresentationContext` property
to true.  This means that the search controller should use this view
controller's frame (as oppposed to the root view controller) when
presenting the search interface.  In this case it means that the search
interface will expand above the carrier bar.

```swift
class ViewController: UIViewController, UITableViewDataSource, UISearchResultsUpdating {
    @IBOutlet weak var tableView: UITableView!

    let data = ["New York, NY", "Los Angeles, CA", "Chicago, IL", "Houston, TX",
        "Philadelphia, PA", "Phoenix, AZ", "San Diego, CA", "San Antonio, TX",
        "Dallas, TX", "Detroit, MI", "San Jose, CA", "Indianapolis, IN",
        "Jacksonville, FL", "San Francisco, CA", "Columbus, OH", "Austin, TX",
        "Memphis, TN", "Baltimore, MD", "Charlotte, ND", "Fort Worth, TX"]

    var filteredData: [String]!

    var searchController: UISearchController!

    override func viewDidLoad() {
        super.viewDidLoad()

        tableView.dataSource = self
        filteredData = data

        // Initializing with searchResultsController set to nil means that
        // searchController will use this view controller to display the search results
        searchController = UISearchController(searchResultsController: nil)
        searchController.searchResultsUpdater = self

        // If we are using this same view controller to present the results
        // dimming it out wouldn't make sense.  Should set probably only set
        // this to yes if using another controller to display the search results.
        searchController.dimsBackgroundDuringPresentation = false

        searchController.searchBar.sizeToFit()
        tableView.tableHeaderView = searchController.searchBar

        // Sets this view controller as presenting view controller for the search interface
        definesPresentationContext = true
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCellWithIdentifier("TableCell") as UITableViewCell
        cell.textLabel?.text = filteredData[indexPath.row]
        return cell
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return filteredData.count
    }

    func updateSearchResultsForSearchController(searchController: UISearchController) {
        let searchText = searchController.searchBar.text

        filteredData = searchText.isEmpty ? data : data.filter({(dataString: String) -> Bool in
            return dataString.rangeOfString(searchText, options: .CaseInsensitiveSearch) != nil
        })

        tableView.reloadData()
    }
}
```

Here's what this looks like when running.  Notice that unlike in the
search display controller example, we are using the same table view to
display the search results instead of overlaying of a separate table
view.  However, unlike when working with just the search bar, we still
have the built in animation when transitioning to the search interface.

<a href="http://imgur.com/AgUYVoN"><img src="http://i.imgur.com/AgUYVoN.gif" title="source: imgur.com" /></a>

### Example searching a collection view

We can just as easily use the search controller to search a collection
view in place.   We still have the presentation of a search interface,
but unlike when working with the search display controller we are not
restricted to using a table view to display the search results.

<a href="http://imgur.com/ZEyaPyz"><img src="http://i.imgur.com/ZEyaPyz.gif" title="source: imgur.com" /></a>

The code for this is almost the same as when searching the the table
view above.  The only notable difference is that we had to introduce a
placeholder view in interface builder for the search bar since there are
still some quirks with placing a search controller's search bar inside a
collection view's supplementary view.

```swift
class ViewController: UIViewController, UICollectionViewDataSource, UISearchResultsUpdating {
    @IBOutlet weak var collectionView: UICollectionView!
    @IBOutlet weak var searchBarPlaceholder: UIView!
    ...
    override func viewDidLoad() {
        ...
        searchController.searchBar.sizeToFit()
        searchBarPlaceholder.addSubview(searchController.searchBar)
        automaticallyAdjustsScrollViewInsets = false
        definesPresentationContext = true
    }

    ...
}
```

## Search Bar in Navigation View
A common requirement is to place the search bar inside the navigation
bar.

<a href="http://imgur.com/RDnRxNO"><img src="http://i.imgur.com/RDnRxNO.gif" title="source: imgur.com" /></a>

This can be configured programatically in your view controller's
`viewDidLoad` as follows.

**When working directly with a search bar:**

```swift
        searchBar.sizeToFit()
        navigationItem.titleView = searchBar
```

*Using a search display controller:*

```swift
        searchDisplayController?.displaysSearchBarInNavigationBar = true
```

*Using a search controller:*

```swift
        searchController.searchBar.sizeToFit()
        navigationItem.titleView = searchController.searchBar

        // By default the navigation bar hides when presenting the
        // search interface.  Obviously we don't want this to happen if
        // our search bar is inside the navigation bar.
        searchController.hidesNavigationBarDuringPresentation = false
```

<!--
Search Bar Scopes
-->
