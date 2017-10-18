## Overview

Production applications will often need to perform heavier operations such as downloading high-resolution images or a executing non-cached database queries. To prevent stalling the main thread (and a hit in frame rate), Apple has provided a few tools to help you out! We'll take a look at Grand Central Dispatch, [`NSOperation`](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOperation_class/)s, and the [`performSelectorInBackground`](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/#//apple_ref/occ/instm/NSObject/performSelectorInBackground:withObject:) method on `NSObject`.

## Available Options

1. Grand Central Dispatch
   * Grand Central Dispatch is a technology that abstracts away the low-level details of multithreading. When using GCD, you only have to think about the tasks you want to perform. These tasks can then be added to serial or concurrent queues. Moreover, you can add tasks to groups and run code after all tasks within the group complete!
2. `NSOperation` and `NSOperationQueue`
   * `NSOperation`s and `NSOperationQueue`s provide you with a higher-level API, when compared to GCD. They were first introduced in iOS 4 and are actually implemented with GCD under the hood. Typically, you'll want to use this API over GCD, unless you're performing a simple unit of work on a specific queue. `NSOperation`s provide you with powerful functionality such as cancellation and dependencies.
3. `performSelectorInBackground`
   * If you need to perform a simple unit of work on a new thread, `NSObject` provides you with
  `performSelectorInBackground(_:withObject:)`. Using this, you can run a function (with an argument) on a background thread.

### Grand Central Dispatch

<img src="https://imgur.com/cjRYJZq.png"/>

Let's walk through an example where we download an image from a remote URL and then use it to populate a `UIImageView`.

```swift
// Assume we have an `imageView` property on self
private func loadWallpaper() {
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_BACKGROUND, 0)) { [weak self] in
        guard
            let wallpaperURL = NSURL(string: "http://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-157301.jpg"),
            let imageData = NSData(contentsOfURL: wallpaperURL)
        else {
            return
        }

        dispatch_async(dispatch_get_main_queue()) {
            self?.imageView.image = UIImage(data: imageData)
        }
    }
}
```

- Most uses of GCD start with a call to `dispatch_async`, which takes in a queue to use and the block to execute
- In our example, we'd like to execute the wallpaper download on a background queue, so we make use of the system-defined global queue with a background quality of service (QoS), `DISPATCH_QUEUE_PRIORITY_BACKGROUND`.
  - The flag passed into `dispatch_get_global_queue` should always be 0
- Now we have the block of work to execute
  - We construct a `NSURL` via its failable `String` initializer and then fetch the data associated with that resource via `NSData(contentsOfURL:)`.
- If the above step completes successfully (else we just return from the block), we now have our data at hand!
- To update `imageView`'s `image` property, we need to make sure we return to the main thread via `dispatch_async(dispatch_get_main_queue()) { /* ... */ }`. _Remember in iOS, all UI updates should be performed on the main thread_. Inside the main thread block, we set the image using the `NSData` initializer on `UIImage`.

Now that we've seen a one-off block example, let's dive into how you can accomplish groups of dependent tasks. Imagine you wanted to download multiple wallpapers and present an alert to the user when all of the images finish loading. Dispatch groups will be your best friends in these scenarios!

First, let's refactor the `loadWallpaper` function from the previous example to accept a `dispatch_group_t` and a target URL.

```swift
private func loadWallpaper(group: dispatch_group_t, url: String) {
    dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_BACKGROUND, 0)) { [weak self] in
        defer {
            dispatch_group_leave(group)
        }

        guard
            let wallpaperURL = NSURL(string: url),
            let imageData = NSData(contentsOfURL: wallpaperURL)
        else {
            // In production scenarios, we would want error handing here
            return
        }

        // Use imageData in some manner, e.g. persisting to a cache, present in view hierarchy, etc.
        print("Image downloaded \(url)")
    }
}
```

- The function has been modified slightly to accept a parameter `group` of type `dispatch_group_t` (we'll go into how to create these groups in the next snippet) and a target URL. Additionally, our previous call to `dispatch_async` has been replaced with `dispatch_group_async`, signaling that the block should be associated with `group`. Lastly, after completing our work with the resulting `imageData` we must notify `group` that the block is complete via `dispatch_group_leave`.

To use `loadWallpaper(_:url:)` a call site could look like so:

```swift
private func fetchAllWallpapers() {
        let urls = [
            "http://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-329991.jpg",
            "http://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-329805.jpg",
            "http://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-330201.jpg"
        ]

        let wallpaperGroup = dispatch_group_create()

        urls.forEach {
            dispatch_group_enter(wallpaperGroup)
            loadWallpaper(wallpaperGroup, url: $0)
        }

        dispatch_group_notify(wallpaperGroup, dispatch_get_main_queue()) { [weak self] in
            let alertController = UIAlertController(title: "Done!", message: "All images have downloaded", preferredStyle: .Alert)
            alertController.addAction(UIAlertAction(title: "OK", style: .Default, handler: nil))

            self?.presentViewController(alertController, animated: true, completion: nil)
        }
    }
```

- We start by creating a dispatch group, `wallpaperGroup`, using `dispatch_group_create()`
- With the group in hand, we loop over all of the wallpaper URLs, first signaling to the group that we are about to start an operation by making a call to `dispatch_group_enter(wallpaperGroup)` (each group entry call must pair with a group leave call). We then proceed to call `loadWallpaper(_:url:)`
- To run code after completion of the group, we specify a block in a `dispatch_group_notify` call. In our case, we'll simply present a `UIAlertController` letting the user know that all of the downloads have finished.

While GCD can be extremely powerful, it can be a bit cumbersome to work with in practice. To help with this, [Luo Jie](https://github.com/beeth0ven) came up with a [Swifty GCD wrapper](http://nshipster.com/new-years-2016/#swiftier-gcd) that I often use in my projects:

```swift
protocol ExcutableQueue {
    var queue: dispatch_queue_t { get }
}

extension ExcutableQueue {
    func execute(closure: () -> Void) {
        dispatch_async(queue, closure)
    }
}

enum Queue: ExcutableQueue {
    case Main
    case UserInteractive
    case UserInitiated
    case Utility
    case Background

    var queue: dispatch_queue_t {
        switch self {
        case .Main:
            return dispatch_get_main_queue()
        case .UserInteractive:
            return dispatch_get_global_queue(QOS_CLASS_USER_INTERACTIVE, 0)
        case .UserInitiated:
            return dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0)
        case .Utility:
            return dispatch_get_global_queue(QOS_CLASS_UTILITY, 0)
        case .Background:
            return dispatch_get_global_queue(QOS_CLASS_BACKGROUND, 0)
        }
    }
}

enum SerialQueue: String, ExcutableQueue {
    case DownLoadImage = "myApp.SerialQueue.DownLoadImage"
    case UpLoadFile = "myApp.SerialQueue.UpLoadFile"

    var queue: dispatch_queue_t {
        return dispatch_queue_create(rawValue, DISPATCH_QUEUE_SERIAL)
    }
}
```

Using this wrapper, our example above could be rewritten as:

```swift
Queue.Background.execute {
    guard
        let url = NSURL(string: "http://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-157301.jpg"),
        let data = NSData(contentsOfURL: url)
    else {
        return
    }

    Queue.Main.execute { [weak self] in
        self?.imageView.image = UIImage(data: data)
    }
}
```

### NSOperation

To start, we'll port the wallpaper downloading example to use an `NSBlockOperation`. `NSBlockOperation` is a simple wrapper on a block of work that can be added to a queue.

```swift
private func loadWallpaper(queue: NSOperationQueue, url: String) {
    guard let wallpaperURL = NSURL(string: url) else { return }

    let downloadOperation = NSBlockOperation {
        guard let imageData = NSData(contentsOfURL: wallpaperURL) else { return }

        NSOperationQueue.mainQueue().addOperationWithBlock { [weak self] in
            self?.imageView.image = UIImage(data: imageData)
        }
    }

    queue.addOperation(downloadOperation)
}
```

- The initializer for `NSBlockOperation` simply takes a block to run. In our case, we'll download the data from `wallpaperURL` and return to the main queue to set the `image` property on `imageView`
- After initializing `downloadOperation`, we add it to `queue`

When creating an `NSOperationQueue`, you have a few points of customization

```swift
let queue = NSOperationQueue()
queue.maxConcurrentOperationCount = 1

// If you want to hold the queue, use the `suspended` property
queue.suspended = true
```

- The `maxConcurrentOperationCount` property allows you to set a limit on how many operations may run concurrently in a given queue. Setting this to 1, implies your queue will be serial (queing order may not be preserved, as operations only run when their `ready` flag is set to true). If this property isn't set, it defaults to `NSOperationQueueDefaultMaxConcurrentOperationCount`, which is dictated by system conditions.
- By default, all operations that are ready (`ready` property is true) are run when added to a queue. You can halt all execution on a queue by setting the `suspended` property to true.

`NSOperation`s become really powerful when you separate them out into operation subclasses. To demonstrate this, let's make a wallpaper resizing operation. We'll need to subclass a [custom wrapper](https://gist.github.com/Jasdev/ba9f03747086d4939e55) of `NSOperation` that has the proper KVO notifications in place (see 'Subclassing Notes' in the [docs](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOperation_class/)).

```swift
class ResizeImageOperation: Operation {

    enum Error {
        case FileReadError
        case ResizeError
        case WriteError
    }

    let targetSize: CGSize
    let path: NSURL
    var error: Error?

    init(size: CGSize, path: NSURL) {
        self.targetSize = size
        self.path = path
    }

    override func execute() {
        // Need to signal KVO notifications for operation completion
        defer {
            finish()
        }

        guard let sourceImage = UIImage(contentsOfFile: path.absoluteString) else {
            error = Error.FileReadError
            return
        }

        let finalWidth: CGFloat, finalHeight: CGFloat
        let ratio = sourceImage.size.width / sourceImage.size.height

        // Scale aspect fit the image
        if sourceImage.size.width >= sourceImage.size.height {
            finalWidth = targetSize.width
            finalHeight = finalWidth / ratio
        } else {
            finalHeight = targetSize.height
            finalWidth = finalHeight * ratio
        }

        let imageSize = CGSize(width: finalWidth, height: finalHeight)
        UIGraphicsBeginImageContextWithOptions(imageSize, true, 0.0)
        defer { UIGraphicsEndImageContext() }

        let rect = CGRect(origin: .zero, size: imageSize)
        sourceImage.drawInRect(rect)

        guard
            let resizedImage = UIGraphicsGetImageFromCurrentImageContext(),
            let imageData = UIImageJPEGRepresentation(resizedImage, 1.0)
        else {
            error = Error.ResizeError
            return
        }

        guard imageData.writeToFile(path.absoluteString, atomically: true) else {
            error = Error.WriteError
            return
        }
    }
}
```

- To help with error handling, we add a nested `Error` enum with a few cases.
- `ResizeImageOperation` can be initialized with a target size and path to write,
- The meat of the operation is placed in the `execute` method (overridden from `Operation`). We need to make sure to `defer` a call to `finish()`, so that the `Operation` superclass can signal the proper KVO notifications.
- We then proceed with the resizing the image (scale aspect fit) and saving it to disk.

Now that we have a resizing operation in hand, let's refactor our download operation a bit to work with it:

```swift
private func downloadWallpaper(url: NSURL, path: NSURL) -> NSOperation {
    return NSBlockOperation {
        guard
            let imageData = NSData(contentsOfURL: url),
            let image = UIImage(data: imageData)
        else { return }

        UIImageJPEGRepresentation(image, 1.0)?.writeToFile(path.absoluteString, atomically: true)
    }
}
```

- We now return an `NSOperation` and have the operation write the image data to disk.

Lastly, to make the download and resize operations dependent, we can use them like so:

```swift
// Assume self has `imageView` and `wallpaperQueue` properties

if
    let cacheDirectory = NSSearchPathForDirectoriesInDomains(.CachesDirectory, .UserDomainMask, true).first,
    let cacheDirectoryURL = NSURL(string: cacheDirectory)
{

    let targetURL = cacheDirectoryURL.URLByAppendingPathComponent("wallpaper.jpg")
    let downloadOperation = downloadWallpaper(NSURL(string: "http://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-329991.jpg")!, path: targetURL)

    let resizeOperation = ResizeImageOperation(size: CGSize(width: imageView.bounds.size.width * 2, height: imageView.bounds.size.height * 2), path: targetURL)
    resizeOperation.addDependency(downloadOperation)

    resizeOperation.completionBlock = { [weak self, weak resizeOperation] in
        if let error = resizeOperation?.error {
            print(error)
            return
        }

        guard
            let path = resizeOperation?.path,
            let imageData = NSData(contentsOfFile: path.absoluteString)
        else {
            return
        }

        NSOperationQueue.mainQueue().addOperationWithBlock {
            self?.imageView.image = UIImage(data: imageData)
        }
    }

    wallpaperQueue.suspended = true
    wallpaperQueue.addOperation(downloadOperation)
    wallpaperQueue.addOperation(resizeOperation)
    wallpaperQueue.suspended = false
}
```

- The key line to notice is `resizeOperation.addDependency(downloadOperation)`. That's how we express the resizing operation's dependency on `downloadOperation`.
- Moreover, in the completion block of `resizeOperation`, we check for errors and proceed with displaying the resized image.
- Note: we make sure to suspend the queue first, then add the operations. This prevents the operations from beginning immediately upon addition.

### PerformSelectorInBackground

To wrap up, let's show a simple example of `performSelectorInBackground`. Assuming `self` has a method `sleepAndPrint(_:)`, we can make the following call:

```swift
performSelectorInBackground("sleepAndPrint:", withObject: "supsup")
```

If our target selector had no argument, the selector would simply be `"sleepAndPrint"`).

```swift
func sleepAndPrint(message: String) {
    NSThread.sleepForTimeInterval(1)
    print(message)
}
```

## Key Takeaways

We've used GCD, `NSoperation`s, and `NSObject`'s `performSelectorInBackground` method as means of performing work in a multithreaded fashion. If you have small units of work to perform, you'll want to reach for GCD or `performSelectorInBackground`. On the other hand, if you have larger operations that may have dependencies, `NSOperation` should be your tool of choice. For more info on these topics check out Apple's [Thread Programming Guide](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Multithreading/Introduction/Introduction.html) and the WWDC 2015 session on [Advanced NSOperations](https://developer.apple.com/videos/play/wwdc2015-226/)!

- [@jasdev](https://twitter.com/jasdev)
