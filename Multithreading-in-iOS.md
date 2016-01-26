# Overview 
# Options
- Grand Central Dispatch (w/ dispatch groups)
- `NSOperation` (w/ `NSOperationQueue`)
- `performSelectorInBackground`

# Differences Between the Options

# GCD
- Simple block example (download a wallpaper and return to the main thread to set it)
- More advanced example with doing background work on a queue
- Present a Swift wrapper for GCD to make the APIs nicer to work with

# NSOperation
- Wallpaper Downloading NSOperation
- Image Resizing NSOperation
- Operation dependencies (using the previous two)
- Show progress for an operation
- Show how to enqueue operations

# Wrap Up and Key Takeaways