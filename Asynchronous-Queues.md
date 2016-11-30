# Asynchronous Queues

## Execute code asynchronously in the background
For time consuming tasks such as network calls and reading / writing to disk, it is often a good idea to execute these tasks on a background thread. This allows the task to be handled asynchronously and not block the main thread from handling necessary UI updates.

```swift
DispatchQueue.global().async { 
   // Code to be executed in asynchronously in the background
}
```

## Execute code on the main thread
When a task on an asynchronous background thread has completed and has returned data you want to use, it is a good idea to explicitly execute that code back on the main thread. An example of this might be when you have retrieved data back asynchronously from a network call and now want to represent that data in a UI element, such as setting a label's text.

```swift
DispatchQueue.main.async {
   // Code to be executed on the main thread
}
```
