# Introduction

This page a quick reference guide to Swift. Keep it bookmarked as you begin development, for a quick reminder of the rules and syntax.

This guide trades nuance for brevity. For an in-depth look at the language, check out the official documentation:

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

How *let* affects different types:

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

Arrays may only contain one type, but it can be inferred. If you must be more explicit:

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
let characterDictionary = ["Simba": "Matthew Broderick", "Mufasa": "James Earl Jones", "Scar": "Jeremy Irons"]
```

Similarly, for an empty dictionary:

```
var characterDictionary: [String:String] = Dictionary<String, String>()
characterDictionary["Simba"] = "Matthew Broderick"
```

Dictionary keys must be hashable. However, all of Swift's basic types are hashable.

### Tuples

Tuples are a lightweight way to pass around a set of data. They're like more powerful arrays.

```
let myColors = ("Green", "Blue", "Indigo", "Violet")
println(myColors.2) // "Indigo"
```

They can have named values:

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

Notice the three periods, `...`. That will cover 1 up to 10, a *closed range.* To omit the last item (in this case1 up to 9), use `..<`, a *half range*.

## Optionals and Nil

nil is a big deal. If you message nil, you get a runtime error.

Nil is a special value. The only variable that can contain nil is an "optional." It may be nil or the type you specify. To access that underlying value, you unwrap the optional.

Declare an optional using "?" by the type. Unwrap the underlying value with the "!" operator.

```
let optionalValue : Int? = 1
if optionalValue != nil {
  let intValue = optionalValue!
}
```

Nil is not a boolean value. You must check `optionalValue != nil`. However, there's shorthand:

```
if let intValue = optionalValue {
  println("Int value: \(intValue)")
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

## Functions

```
func functionName(variableName: String){
    println(“Hello \(variableName)”)
}

functionName("Ben") // "Hello Ben"
```

Default Values:

```
func functionName(name: String = "Somebody"){
    println("Hello \(name)!")
}
functionName() // "Hello Somebody"
```

Return values:

```
func greetingGenerator(name: String = "World") -> String {
  return "Hello \(name)"
}

let greeting = buildGreeting()
println(greeting) // "Hello World"
```

For clarity, use keyword parameters:

```
func performGreeting(greeting:String, withName name: String){
    println("\(greeting) \(name).")
}

performGreeting("Hello", withName:"Ben")
```

To use the variable name as the:

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

Just like Ruby, if the last argument is a closure, it doesn't require parenthesis. It's a trailing closure.

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

If you override a method, you must use `override`

```
class Animal {
  func happiness() -> String {
    return ""
  }
}
class Dog: Animal {
  override func happiness() -> String {
    return "Wag tail"
  }
}
```

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

If it's just a getter

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

Property Observers

```
class Dog: Animal {
  var cute = true
  var grownUp:Bool = false {
    willSet {
      println("Puppy is growing up")
    }
    didSet(oldValueForGrownUp) {
      if (grownUp){
        cute = false
      } else {
        cute = true
      }
    }
  }
}
var myDog = Dog()
myDog.cute            // True
myDog.grownUp = true
myDog.cute            // False
```

If the new values aren't specific in the method name, `newValue` and `oldValue` are special keywords

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

They're like C structs, but much more powerful. They have:

* Methods and computed properties
* Initializers
* They can adhere to protocols

They are not classes:

* No subclassing
* Classes are passed by reference. Structs are passed by value.
* They have an initializer, but not an deinitializer.

```
struct Rect {
  var origin: Point
  var size: Size
  var area: Double {
     return size.width * size.height
  }
}
```

To obey rules around var vs let, make sure mutating properties use the `mutating` keyword

```
struct Point {
  var x: Double
  var y: Double
  mutating func moveToTheRight(dx: double){
    x += dx
  }
}
```

By default, structs come with a member initializer.

```
let myPoint = Point(x: 20.0, y:20.0)
```

## Enum

```
enum Planet: Int {
  case Mercury = 1, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Neptune
}
let earthValue = Planet.Earth
println("Earth raw value: \(earthValue.toRaw()).")

```

They can be other values

```
enum ControlCharacters: Character {
  case Tab = "\t"
  case Linefeed = "\n"
  case CarriageReturn = "\r"
}
```

They can have no raw value

```
enum CompassPoint {
  case North, South, East, West
}
```

Their value can be inferred based on the type of enum

```
var directionToHead CompassPoint.West
direction = .East
```

From UIKit:

```
let label = UILabel
label.textAlignment = .Right
```

They can also have associated values:

```
enum TrainStatus {
  case OnTime
  case Delayed(Int)
}

let status = TrainStatus.Delayed(5)
```

They may contain methods, including initializers

```
enum TrainStatus {
  case OnTime, Delayed(Int)
  init() {
    self = OnTime
  }
  var description : String {
    switch self {
      case OnTime:
        return "On Time."
      case Delayed(let minutes):
        return "Delayed by \(minutes) minutes."
    }
  }
}
```

### Nesting

You can nest an enum to tie it to the class.

```
class Train {
  enum Status {
    // Same as before
  }
  var status = Status()
}
```

Notice that `Train` is removed, since the class provides a namespace. It's just `Status`.

## Extensions

You may extend classes, structs, and enums, without touching the original source code. It is similar to a category in Objective-C, or monkey patching in Ruby.

```
extension Size {
  mutating func increaseByFactor(factor: Int) {
    width *= factor
    height *= factor
  }
}
```

Even primitives work:

```
extension Int {
   func repetitions(task: () -> ()) {
      for i in 0..self {
        task()
      }
   }
}
```

## Generics

An advanced feature. One way of generalizing a method is by accepting the “Any” type, but that loses type information. Generics allow you to retain type safety while generalizing a method.

```
struct Stack<T> {
  var elements = [T]()
  mutating func push(element: T){
    elements.append(element)
  }
  mutating func pop() -> T {
    return elements.removeLast()
  }
}
```

