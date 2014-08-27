# Introduction

This page a quick reference guide the Swift Basics. Anything that can be built with Objective-C can be built with these core parts of the language. To write something more elegant, move on to [[Intermediate Swift|Swift Intermediate]]

This guide trades nuance for brevity. Keep this page bookmarked for a quick reminder of the rules and syntax. For an in-depth look at the language, check out the official documentation:

* [WWDC 2014: Introduction to Swift](https://developer.apple.com/videos/wwdc/2014/#402)
* [iOS Developer Library: A Swift Tour](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/GuidedTour.html)
* [iBooks: The Swift Programming Language](https://itunes.apple.com/us/book/swift-programming-language/id881256329)

## Declarations

### let and var

`let` declares a constant. `var` declares a variable, which is mutable. Use `let` wherever possible. It's safer and it allows compiler optimizations.

```
var mutableNumber = 0
mutableNumber = mutableNumber + 1 // Allowed

let immutableNumber = 0
immutableNumber = immutableNumber + 1 // Illegal
```

How `let` affects different types:

* Dictionaries: You cannot change its keys or values
* Arrays: You cannot change, add, or remove elements
* Strings: Totally immutable
* Structs: All properties are immutable
* Objects: Properties *are mutable*, unless declared with `let`

### Type

```
let i: Int = 0
```

Read left-to-right, “the variable i is an Int.”

You can omit the type if the compiler can infer it:

```
let i = 0
```

You can use `typealias` to create complex types, similar to C's typedef.

```
typealias StatusCode = Int
let okResponse: StatusCode = 200
```

## Primitives

### Strings

```
let string = "Hello World"
```

String interpolation:

```
let namedString = "Hello \(name)"
```

### Numbers

```
let myNumber = 21
```

*myNumber* is of type `Int`. If you'd prefer a float:

```
let myNumber: Float = 21
```


### Bool

```
let myTrueValue = true
let myFalseValue = false
```

### Arrays

```
let myArray = ["Red", "Orange", "Yello"]
```

Arrays may only contain one type, which can be inferred. If you must be more explicit:

```
let myArray : [String] = ["Red", "Orange", "Yello"]
```
    
To initialize an empty array:

```
var myEmptyArray = [String]()

myEmptyArray.append("Red")
```

### Dictionaries

```
let characterDictionary = ["Simba": "Matthew Broderick", "Mufasa": "James Earl Jones"]
```

Similarly, for an empty dictionary:

```
var characterDictionary: [String:String] = Dictionary<String, String>()
characterDictionary["Simba"] = "Matthew Broderick"
```

Dictionary keys must be hashable. However, all of Swift's basic types are hashable.

### Tuples

Tuples are a lightweight way to pass around a group of data. They're like more powerful arrays.

```
let myColors = ("Green", "Blue", "Indigo", "Violet")
println(myColors.2) // "Indigo"
```

They can use named values:

```
let myResponse: (code: Int, message: String) = (200, "OK")
println(myTuple.message)
```

## Control Flow

### If Statements

Parenthesis are optional, but braces are mandatory

```
if booleanValue {
  println("This was true")
}
```

Only `Bool` values may be used. Unlike some languages, `0` and `1` are not equivalent to boolean values. You must be explicit in checking values.

```
if numberValue == 1 {
  println("The value was 1.")
}
```

### Switch

`switch` is much safer than in other languages. They don't fall through, so there no need for `break`. They must be exhaustive, either handling all values, or including `default`.

```
switch someValue {
   case 1:
     println("Hit single value.")
   case 2...10:
     println("Large value.")
   default:
     println("Some other value.")
}
```

### Loops


for/in with an array:

```
for user in arrayOfUsers {
  println(user)
}
```

With a dictionary:

```
for (key, value) in dictionary {
  println("\(key): \(value)")
}
```

With a range:

```
for index in 1...10 {
  println("Index: \(index)")
}
```

Notice the three periods, `...`. That will cover 1 to 10, a *closed range.* To omit the last value (in this case, 1 to 9), use `..<`, a *half range*.

There are also the traditional `for`, `while`, and `do/while` loops:

```
for var i = 0; i < 10; i++ {
    
}

var j = 0
while j < 10 {
    j++
}

var k = 0
do {
    k++
} while k < 10

```

## Nil and Optionals

`nil` represents the absence of a value. If you message `nil`, you get a runtime error, so it should be handled carefully.

The only variable that can contain `nil` is an *optional.* It may be nil or the type you specify. To access the underlying value, you unwrap it.

Declare an optional using `?` by the type. Unwrap the underlying value with the `!` operator.

```
let optionalValue : Int? = 1
if optionalValue != nil {
  let intValue = optionalValue!
}
```

`nil` is not a boolean. You must check `optionalValue != nil`. However, there's shorthand:

```
let optionalValue: Int? = 1
if let intValue = optionalValue {
  println("The int was \(intValue)")
} else {
  println("The int was not there.")
}
```

If you have a chain of properties, rather than unwrap each individually, you can use the "?" operator. For instance:

```
if object != nil && object!.childObject != nil {
  object!.childObject!.method()
}
// Equivalent
object?.childObject?.method()
```

Often, when your optional is nil, you want to use another value. For brevity, you can use *nil coalescing* via the `??` operator. The following two lines are equivalent:

```
a != nil ? a! : b
a ?? b
```

## Functions

```
func functionName(){
    println(“Hello World”)
}

functionName() // "Hello World"
```

With parameters:

```
func functionName(variableName: String){
    println(“Hello \(variableName)”)
}

functionName("Ben") // "Hello Ben"
```

With return values:

```
func greetingGenerator(name: String) -> String {
  return "Hello \(name)"
}

let greeting = buildGreeting()
println(greeting) // "Hello World"
```

With default Values:

```
func functionName(name: String = "Somebody"){
    println("Hello \(name)!")
}
functionName() // "Hello Somebody"
```

For clarity, use keyword parameters:

```
func performGreeting(greeting:String, withName name: String){
    println("\(greeting) \(name).")
}
performGreeting("Hello", withName:"Ben")
```

To use the same keyword name as the variable name:

```
func performGreeting(greeting:String, #name: String){
    println("\(greeting) \(name).")
}

performGreeting("Hello", name:"Ben")
```

### Closures

Functions are just named closures.

```
var greetingClosure: (String, String) -> (String) = {
    (greeting, name) in
    return "\(greeting) \(name)."
}

greetingClosure("Hello", "Ben")

```

If the last argument is a closure, it doesn't require parenthesis, much like Ruby. These are *trailing closures.*

```
repeat(2) {
  println("Hello!")
}
```

## Classes

```
class Animal {

}
```

### Subclassing

```
class Dog: Animal {
    
}

var myDog = Dog()

```

### Methods

```
class Dog: Animal {
  func bark() -> String {
    return "Woof"
  }
}
```

You must use `override` to override a method.

```
class Animal {
  func happiness() -> String {
    return "This animal does not get happy."
  }
}
class Dog: Animal {
  override func happiness() -> String {
    return "Wag tail"
  }
}
```

To call the super method, use `super.nameOfMethod()`

### Properties

There is no difference between an ivar and property.

```
class Dog: Animal {
    var cute = false
    func bark() -> String {
        if cute {
            return "Woof"
        } else {
            return "Growl"
        }

    }
}

var myDog = Dog()
myDog.bark()       // "Growl"
myDog.cute = true
myDog.bark()       // "Woof"
```

For computed setters and getters:

```
class Dog: Animal {
  var cute = false
  var adorable: Bool {
    get {
        return cute
    }
    set(newAdorable) {
        cute = newAdorable
    }
  } 
}

var myDog = Dog()
myDog.cute = true
myDog.adorable    // true
```

### Initializers and Deinitializers

The initializer must make sure every stored property has a value before any methods are called, including `super.init()`

```
class Dog: Animal {
  var cute: Bool
  override init() {
    cute = true
    super.init()
  }
}
```

This is equivalent to the default initializer for:

```
class Dog: Animal {
  var cute = true
}
```

However, by overriding the initializer, you lose the default assignment behavior for all properties.

To perform cleanup code before an object is destroyed:

```
class Dog: Animal {
    deinit {
       println("Cleaned up")
    }
}
```

## Structs

Swift structs are like C structs, but much more powerful, resembling classes. These advanced features are covered in [[Intermediate Swift|Swift Intermediate]].

```
struct User {
    var name: String
    var occupation: String
}
```

By default, structs come with a member initializer.

```
let ben = User(name: "Ben Sandofsky", occupation:"Engineer")
```

Unlike a class, when a struct is declared with `let`, all of its properties are immutable.

As with Objective-C, structs are passed by value, classes are passed by reference.

## Enum

Like structs, Enums are more powerful than their C equivalents. See [[Intermediate Swift|Swift Intermediate]].

```
enum Color: Int {
  case Red = 1, Orange, Yellow, Green, Blue, Indigo, Violet
}
let orangeValue = Color.Orange
```

To access the underlying value, use `toRaw()`:

```
println("Orange raw value: \(orangeValue.toRaw()).")
```

Enums may use other underlying values:

```
enum ControlCharacters: Character {
  case Tab = "\t"
  case Linefeed = "\n"
  case CarriageReturn = "\r"
}
```

They can have no raw value:

```
enum Season {
  case Spring, Summer, Fall, Winter
}
```

If the enum type can be inferred, you can omit it.

```
let label = UILabel
label.textAlignment = .Right
```

## Extensions

You may extend classes, structs, and enums, without touching the original source code. It is similar to a category in Objective-C, or monkey patching in Ruby.

```
extension String {
    func tweetable() -> Bool {
        return countElements(self) <= 140
    }
}
```
