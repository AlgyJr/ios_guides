## Overview

This guide covers the main Swift concepts you'll need as you start building apps. It is meant to introduce each topic in a way that provides enough background to get you started and defers to the [iBook on Swift](https://itunes.apple.com/us/book/swift-programming-language/id881256329) for more in depth coverage.

## Hello World

It is common to start any tutorial for a new language with the **Hello World** example so we'll start out by showing how easy this is to do in Swift:

```swift
print("Hello World!")      // Prints "Hello World!" to the output window in Xcode
```

To make this a little more personal, let's look at one of the handy features of Swift (called **string interpolation**) where we can insert values into strings using the `\(...)` syntax. Below, we've inserted `name` into the middle of our string:

```swift
let name: String = "Bob"
print("Hello \(name)!")     // Prints "Hello Bob!"
```

## When to use "let" vs "var"

You'll notice in the example above we've used `let` to create the new variable for the name "Bob". In Swift, you'll choose from the following 2 options when creating a new variable:

* `let` => Use `let` when you are defining a **constant** (a value that will not change)

    ```swift
    let numberOfContinents: Int = 7   // Seven continents will not change so we've used "let"
    ```
* `var` => Use `var` when you are defining a **variable** (a value that might change)

    ```swift
    var continentsVisited: Int = 2   // Continents visited increases over time so we've used "var"
    ```

In general, it is considered a best practice to use constants (`let`) whenever possible.

## Numbers

The two most common types of numbers you'll use in Swift are integers (`Int`) and doubles (`Double`):

Integers are whole numbers with no fractional component:

```swift
let minValue: Int = -42
let maxValue: Int = 55 
```

Doubles can have a fractional component:

```swift
let pi: Double = 3.14159
let billAmount: Double = 10.25
```

## Strings

Strings represent a series of characters:

```swift
let hello: String = "Hello"
let world: String = "World"
```

You can use **string interpolation** to construct new strings:

```swift
let helloWorld: String = "\(hello) \(world)"    // "Hello World"
```

Or you can use the `+` operator to combine strings:

```swift
let helloWorld: String = hello + " " + world    // "Hello World"
```

## Booleans

Boolean is a very simple type in Swift as it can only ever be `true` or `false`:

```swift
let swiftIsCool: Bool = true
let iMissObjectiveC: Bool = false 
```

## Arrays

Arrays store a list of values that must be of the same type. Below we've kept track of the previous bill amounts (which is a list of doubles):

```swift
// Notice how we've used "var" here since we want to append new items to the array
var previousBillAmounts: [Double] = [10.25, 21.32, 15.54]
```

To add a new bill to the array:

```swift
previousBillAmounts.append(52.45)    // Result: [10.25, 21.32, 15.54, 52.45]
```

To check on how many bills there are in the array:

```swift
let count = previousBillAmounts.count            // Result: 4
```

Or to check the first bill amount in the array:

```swift
let firstBillAmount = previousBillAmounts[0]    // Result: 10.25
```

## Dictionaries

Much like you might use a real world dictionary to look up the definition for a word, you can use the dictionary data structure in Swift to create associations between keys (the word in the real world dictionary) and values (the definition in the real world dictionary).

This is easiest seen using an example. Let's say we want to keep track of the ages of people that are using our app. This would be best done using the following dictionary:

```swift
var people: [String: Int] = [
                              "Bob": 32,
                              "Cindy": 25
                            ]
```

Here, the **key** is the name of the person and the **value** is the person's age.

Later on if we want to find out Bob's age, we can look it up by doing:

```swift
let bobsAge = people["Bob"]
```

If we get a new user we can easily add them to our dictionary using the following syntax:

```swift
people["Dan"] = 56
```

## Specifying Types

In most of the examples in this guide, we are explicit with the types of our constants and variables. Explicit typing looks something like:

```swift
let name: String = "Bob"   // Explicitly designating "name" to be of type "String"
```

However, Swift is smart enough to infer the type for us in a lot of cases. The previous example can be shortened to:

```swift
let name = "Bob"   // Swift infers "name" is of type "String" since "Bob" is a String
```

In cases where Swift can infer the type, it's not necessary to be explicit with the types for your constants and variables. We just do it in this guide so that the examples are easier to follow.

## Any and AnyObject

Swift has two special "catch all types" that come in handy when a more specific type cannot be determined.

* `AnyObject` can represent an instance of **any class type**.
* `Any` can represent an instance of **any type at all**.

In general it's a best practice to be as specific with your types as possible and avoid the use of `AnyObject` and `Any`, but they become particularly helpful when interacting with Objective-C code that is less strict on typing.

## Optionals

Optionals is a very important concept in Swift and is meant to improve the safety of Swift code. By simply placing a question mark (?) after any type, it declares that variable to be optional. An optional type allows that variable to exist in one of the following two states:

1. There **is a value and it equals x**
2. There **isn't a value at all**

Let's look at an example to make this more clear. Consider the following 2 examples where we are trying to convert a `String` to an `Int`:

```swift
// Example 1 (Conversion succeeds)
let input: String = "123"
let optionalConvertedInput: Int? = Int(input)  // optionalConvertedInput = 123

// Example 2 (Conversion fails - input is not a number)
let input: String = "123abc"
let optionalConvertedInput: Int? = Int(input)  // optionalConvertedInput = nil
```

Swift requires `optionalConvertedInput` to be of type `Int?` (or "optional Int") so that it is explicit that convertedInput *might* not contain a value (in the case when the conversion fails). If we were to declare `convertedInput` as simply `Int`, we'd get a compile error.

There's a handy syntax in Swift that you'll use quite often when working with optionals. If we wanted to use the value of `optionalConvertedInput` later on in our code, we'd have to first check to make sure it's not `nil`. We can do so using the following code:

```swift
if let convertedInput = optionalConvertedInput {
   // Code that gets executed when optionalConvertedInput is NOT nil
} else {
   // OptionalConvertedInput IS nil, do something else
}
```

In other languages like Java, NullPointerException's (exceptions that are fired when a null reference is accessed) are a common source of crashes. Swift's Optionals goes a long way to reduce this type of programming error.

## Functions

Functions in Swift are very similar to other languages. The simplest function in Swift can be written as:

```swift
// This prints "Hello!" to the output window
func printHello() {
    print("Hello!")
}

// Calls this function
printHello()
```

We can extend this function to be a little more personable by taking in a name and returning the greeting instead of printing it out:

```swift
// Takes in a "personName" parameter of type String and returns the greeting as a String
func sayHello(personName: String) -> String {
    return "Hello \(personName)!"
}

// Calls this function
let greeting: String = sayHello("Bob")
```

Things get a little more interesting when you start to have multiple parameters as Swift has the concept of **external** and **local** parameter names. An external parameter name is used to label arguments passed to a function call. A local parameter name is the name used in the implementation of the function.

```swift
// "to" + "and" are the external parameter names
// "person" + "anotherPerson" are the local parameter names
func sayHello(to person: String, and anotherPerson: String) -> String {
    return "Hello \(person) and \(anotherPerson)!"
}

// Calls this function using the "external" parameter names
let greeting: String = sayHello(to: "Bill", and: "Ted")
```

## Control Flow

### Conditional Statements

If statements are very similar to other languages and can be expressed as:

```swift
let temperatureInFahrenheit: Int = 90

if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else if temperatureInFahrenheit >= 86 {
    print("It's really warm. Don't forget to wear sunscreen.")
} else {
    print("It's not that cold. Wear a t-shirt.")
}
```

### Loops

The two most common types of loops you'll need in Swift are **for loops** and **for-in loops**. 

**For loops** work well when you want to do something until a particular condition is met (in the case below until `index >= 3`):

```swift
// Simple for loop that prints "Hello" 3 times
for var index = 0; index < 3; index++ {
    print("Hello")
}
```

**For-in loops** come in really handy when you want to do something to each item in a collection (such as an array):

```swift
let names = ["Anna", "Alex", "Brian", "Jack"]

// Loops over each name in "names" and prints out a greeting for each person
for name in names {
    print("Hello, \(name)!")
}
```

Sometimes you want to loop over each item in an array and also keep track of the index of the item. Array's `enumerate()` method can help you achieve this:

```swift
let names = ["Anna", "Alex", "Brian", "Jack"]

for (index, value) in names.enumerate() {
    print("Item \(index + 1): \(value)")
}
```

## Classes

Classes are the building blocks of your app’s code. You define properties and methods to add functionality to your classes by using the same syntax as for variables and functions.

Below you can find a `Person` class that is meant to show an example of the types of things you'll want to do when building your classes.

```swift
class Person {
	
    // Custom initializer - takes in firstName and lastName
    init(firstName: String, lastName: String) {
        self.firstName = firstName
        self.lastName = lastName

        // Increment type property each time a new person is created
        Person.numberOfPeople++
    }
	
    // *** Properties ***
	
    // Stored Property - Stored as part of the current instance of the class
    var firstName: String
    var lastName: String

    // Computed Property - computes "fullName" from "firstName" and "lastName"
    var fullName: String {
    	get {
            return "\(firstName) \(lastName)"
    	}
    }

    // Type Property - Single instance for all instances of the class,
    // similar to a static property in Java
    static var numberOfPeople = 0   
    
    // *** Methods ***
    
    // Instance Method
    func greet() {
    	// Notice the use of "self" - self refers to the current instance and 
        // is similar to "this" in Java
    	print ("Hello \(self.firstName)")
    }
    
    // Type Method
    class func printNumberOfPeople() {
    	print("Number of people = \(Person.numberOfPeople)")
    }
}

// ... Using the Person Class ...

// Create a new instance of the Person class
let bob = Person(firstName: "Bob", lastName: "Smith")

// Call instance method
bob.greet()   // Prints "Hello Bob"

// Accessing properies
print("Bob's first name is: \(bob.firstName)")  // Prints "Bob's first name is: Bob"
print("Bob's full name is: \(bob.fullName)")    // Prints "Bob's full name is: Bob Smith"

// Call type method
// Prints "Number of people = 1" (since we've only created one Person)
Person.printNumberOfPeople()
```

## Protocols

Protocols are similar to interfaces in other languages. Think about a protocol as a contract. The contract includes a set of methods that must be implemented. Any classes that choose to implement a protocol sign this contract and implement all the methods that are in the protocol.

Let's say we have the following protocol (`MyFriendlyGreeterProtocol`) that defines 2 methods `sayHello()` and `sayBye()`:

```swift
protocol MyFriendlyGreeterProtocol {
    func sayHello()
    func sayBye()
}
```

Any classes that implement this protocol (you implement a protocol by adding its name after the class defintion as shown below), must implement both of these methods:

```swift
class MyEnglishPerson: MyFriendlyGreeterProtocol {
    func sayHello() {
        print("Hello!")
    }

    func sayBye() {
        print("Bye!")
    }
	
    // ... other methods for this class ...

}

class MySpanishPerson: MyFriendlyGreeterProtocol {
    func sayHello() {
        print("Hola!")
    }

    func sayBye() {
        print("Adios!")
    }
	
    // ... other methods for this class ...
}
```

There is a lot more you can do with protocols as they form one of the key design patterns in iOS. The code above merely shows how to get started with the syntax for protocols.

## Closures

Closures are self-contained blocks of code that can be passed around and used in your code. Closures in Swift are similar to blocks in Objective-C and lambdas in other programming languages.

Closures can capture and store references to any constants and variables from the context in which they are defined. This is known as "closing" over those constants and variables. Swift handles all of the memory management of capturing for you.

Here’s an example of a function called `makeIncrementer`, which contains a nested function called `incrementer`. The nested `incrementer()` function captures two values, `runningTotal` and `amount`, from its surrounding context. After capturing these values, `incrementer` is returned by `makeIncrementer` as a closure that increments `runningTotal` by amount each time it is called.

```swift
func makeIncrementer(forIncrement amount: Int) -> () -> Int {
    var runningTotal = 0
    func incrementer() -> Int {
        runningTotal += amount
        return runningTotal
    }
    return incrementer
}
```

The return type of `makeIncrementer` is `() -> Int`. This means that it returns a function, rather than a simple value. The function it returns has no parameters, and returns an Int value each time it is called.

The `makeIncrementer(forIncrement:)` function defines an integer variable called `runningTotal`, to store the current running total of the incrementer that will be returned. This variable is initialized with a value of 0.

The `makeIncrementer(forIncrement:)` function has a single Int parameter with an external name of `forIncrement`, and a local name of `amount`. The argument value passed to this parameter specifies how much `runningTotal` should be incremented by each time the returned `incrementer` function is called.

`makeIncrementer` defines a nested function called `incrementer`, which performs the actual incrementing. This function simply adds `amount` to `runningTotal`, and returns the result.

When considered in isolation, the nested `incrementer()` function might seem unusual:

```swift
func incrementer() -> Int {
    runningTotal += amount
    return runningTotal
}
```

The `incrementer()` function doesn’t have any parameters, and yet it refers to `runningTotal` and `amount` from within its function body. It does this by capturing a reference to `runningTotal` and `amount` from the surrounding function and using them within its own function body. Capturing by reference ensures that `runningTotal` and `amount` do not disappear when the call to `makeIncrementer` ends, and also ensures that `runningTotal` is available the next time the `incrementer` function is called.

Here’s an example of `makeIncrementer` in action:

```swift
// This example sets a constant called incrementByTen to refer to an incrementer function that
// adds 10 to its runningTotal variable each time it is called.
let incrementByTen = makeIncrementer(forIncrement: 10)

incrementByTen()  // returns a value of 10
incrementByTen()  // returns a value of 20
incrementByTen()  // returns a value of 30
```

## Type Casting (as, as?, as!)

Type casting changes the type of a particular instance to another compatible type. There are 3 ways to accomplish this with Swift:

   1. **Guaranteed conversion** with `as` => This is the safest cast. It will never fail since the compiler can guarantee the cast will work. Use this when you are upcasting from a child class to its parent or doing something like `1 as Float`.

      ```swift
      // Guaranteed conversion as the compiler can verify this will succeed
      let myFloat = 1 as Float

      // Guaranteed conversion as upcasting from a type to its parent type is safe 
      // UIView is a parent of UITableView
      let myView = myTableView as UIView
      ```
   2. **Conditional conversion** with `as?` => This is a cautious cast. If the cast fails, it will return `nil`. This is needed when downcasting from a parent type to a child type.

      ```swift
      // If myView is actually a tableView, the downcast will succeed, otherwise it will fail safely
      if let myTableView = myView as? UITableView {
          print("The downcast succeeded!")
      } else {
          print("The downcast failed!")   	   
      }
      ```
   3. **Forced conversion** with `as!` => This is a dangerous cast that you should avoid using. If the cast fails, this will crash your app. Use this cast carefully.

      ```swift
      // DANGEROUS: If myView is actually a tableView, the downcast will succeed
      // Otherwise it will crash the app
      let myTableView = myView as! UITableView
      ```

## Understanding the Question Mark (?)

There are various places where you'll come across the need for using question marks in your code. The following examples are meant to capture the major types of use cases for the question mark operator that can cause confusion when first learning Swift.

When defining a type as **optional**:

```swift
// Designates that optionalName can either have a value or be nil
var optionalName: String? = "Hello"
```

When safely accessing methods or properties (called **optional chaining**):

```swift
// When myInstance IS nil, x = nil
// When myInstance is NOT nil, x = myInstance.count
// If any part of the chain is nil, it stops evaluating and returns
// nil, ignoring everything to the right.
let x = myInstance?.count
```

When type casting (called **safe conditional conversion**):

```swift
// If myView is actually a tableView the downcast will succeed, otherwise it will fail safely
if let myTableView = myView as? UITableView {
    print("The downcast succeeded!")
} else {
    print("The downcast failed!")   	   
}
```

When using the **nil coalescing operator** (`??`) to provide a default value:

```swift
let nickName: String? = nil  // nickName is an optional that might be nil
let fullName: String = "Bob Smith"

// prints the nickName if it is NOT nil, otherwise prints fullName
let informalGreeting = "Hi \(nickName ?? fullName)"   // Prints "Hi Bob Smith"
```

## Understanding the Exclamation Mark (!)

There are various places where you'll come across the need for using exclamation marks in your code. The following examples are meant to capture the major types of use cases for the exclamation mark operator that can cause confusion when first learning Swift.

When getting the actual value out of an optional (called **unwrapping an optional**):

```swift
let possibleString: String? = "An optional string."

// DANGEROUS: possibleString must NOT be nil or this will crash
let forcedString: String = possibleString! 

// SAFE: Will only enter the if clause if possibleString is NOT nil
if let actualString = possibleString {
    // do something with actualString
}
```

When type casting (called **forced conversion**):

```swift
// DANGEROUS: If myView is actually a tableView, the downcast will succeed
// Otherwise it will crash the app
let myTableView = myView as! UITableView
```

When defining variables that are initially `nil` but get set soon afterwards and are guaranteed not to be nil after that (called **implicitly unwrapped optionals**):

```swift
let assumedString: String! = "An implicitly unwrapped optional string."

// no need for an exclamation mark since assumedString is an implicitly unwrapped optional
let implicitString: String = assumedString
```

## Working with JSON

A lot of the time when working with REST API's (like Instagram, Twitter, etc), the data that comes back will be JSON. JSON is a human readable data format (very similar to XML).

Below is an example of JSON that simulates the type of JSON you might get back when using an endpoint that returns movies and their ratings:
  * An open curly brace means the start of a dictionary
  * An open bracket means the start of an array

```json
// Sample JSON you might get back from a movie rating API:
{
    "status": "OK",
    "movies": [
        {
            "title": "Whiplash",
            "rating": 8.5
        }, 
        {
            "title": "Feast",
            "rating": 5.2
        }, 
        {
            "title": "Kung Fury",
            "rating": 7.1
        }
    ]
}
```

Below is an example of how'd you extract the movies and ratings out of that response:

```swift
// data returned from the network response will typically be of type
// NSData (which is a buffer of bytes)
let responseData: NSData = // ... some value retrieved from the network response ...

// Wrap our code in a do catch as our code might throw an exception which we need to handle
do {
    // Start by converting the NSData to a dictionary - a dictionary for the entire response
    if let responseDictionary = try NSJSONSerialization.JSONObjectWithData(responseData,
        options:NSJSONReadingOptions(rawValue:0)) as? [String:AnyObject] {

            // Dip inside the response to find the "movies" key and get the array of movies
            if let movies = responseDictionary["movies"] as? [AnyObject] {

                // Get each movie dictionary from the array of movies
                for movie in movies {
                
                    // Use the movie "title" key and "rating" key to get their values
                    if let title = movie["title"] as? String {
                        if let rating = movie["rating"] as? Double {
                            print("Title:\(title), rating:\(rating)")
                        }
                    }
                }
            }
    }
} catch {
    print("Error parsing JSON")
}
```

## Playgrounds

Xcode includes a very useful tool for learning Swift called "Playgrounds". It's very easy to [create a new playground](https://developer.apple.com/library/ios/recipes/Playground_Help/Chapters/CreateAndEdit.html#//apple_ref/doc/uid/TP40015166-CH36-SW1) through Xcode.

Once inside of a playground, you can write Swift code and see it run immediately (without needing to build and run a project each time). This allows you to try out different syntax and test out your code before including it into your app.

We highly recommend checking out Playgrounds while you are learning Swift.

## References
* [WWDC 2014: Introduction to Swift](https://developer.apple.com/videos/wwdc/2014/#402)
* [iOS Developer Library: A Swift Tour](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/GuidedTour.html)
* [iBooks: The Swift Programming Language](https://itunes.apple.com/us/book/swift-programming-language/id881256329)
* [Swift Type Casting](https://www.murage.ca/downcasting-in-swift-1-2-with-as-exclamation)