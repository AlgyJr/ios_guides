# Introduction

This is quick guide to Swift Basics. Anything that can be built with Objective-C can be built with these core parts of the language. More elegant features are covered in [[Intermediate Swift|Swift Intermediate]]

Use this guide as a quick reference to basic syntax and rules. For an in-depth look at the language, check out the official documentation:

* [WWDC 2014: Introduction to Swift](https://developer.apple.com/videos/wwdc/2014/#402)
* [iOS Developer Library: A Swift Tour](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/GuidedTour.html)
* [iBooks: The Swift Programming Language](https://itunes.apple.com/us/book/swift-programming-language/id881256329)

## Declarations

### let and var

`let` declares a constant. `var` declares a variable, which is mutable. Use `let` wherever possible. It's safer and allows compiler optimization.

```Swift
var mutableNumber = 0
mutableNumber = mutableNumber + 1 // Allowed

let immutableNumber = 0
immutableNumber = immutableNumber + 1 // Illegal
```

### Type

```Swift
let i: Int = 0
```

Read left-to-right, “The constant i is an Int.”

You can omit type if the compiler can infer it:

```Swift
let i = 0
```

Use `typealias` to create complex types, similar to C's `typedef`.

```Swift
typealias StatusCode = Int
let okResponse: StatusCode = 200
```

## Primitives

### Strings

```Swift
let name = "Ben"
let interpolatedString = "Hello \(name)"    // "Hello Ben"
let concatenatedString = "Hello" + " world" // "Hello world"
```

### Numbers

```Swift
let myNumber = 21                     // Results in an Int
let myFloat: Float = 21               // Results in a Float
let floatConversion = Float(myNumber) // Converts to a Float
```

### Bool

```Swift
let myTrueValue = true
let myFalseValue = false
```

Only booleans are true and false. False is *not* equivalent to `0`, an empty string, or `nil`.

### Arrays

```Swift
let myArray = ["Red", "Orange", "Yellow"]
```

Arrays may only contain one type, which can be inferred. If you must be more explicit:

```Swift
let myArray : [String] = ["Red", "Orange", "Yellow"]
```
    
To initialize an empty array:

```Swift
var myEmptyArray = [String]()
myEmptyArray.append("Red")
```

### Dictionaries

```Swift
let characterDictionary = ["Simba": "Matthew Broderick", "Mufasa": "James Earl Jones"]
```

Similarly, for an empty dictionary:

```Swift
var characterDictionary: [String:String] = Dictionary<String, String>()
characterDictionary["Simba"] = "Matthew Broderick"
```

Dictionary keys must be hashable. However, all of Swift's basic types (`String`, `Int`, `Bool`, etc) are hashable.

### Tuples

Tuples are a lightweight way to pass around a group of data. They're like more powerful arrays.

```Swift
let myColors = ("Green", "Blue", "Indigo", "Violet")
println(myColors.2) // "Indigo"
```

They can use named values:

```Swift
let myResponse: (code: Int, message: String) = (200, "OK")
println(myTuple.message)
```

## Control Flow

### If

Parenthesis are optional, but braces are mandatory

```Swift
if booleanValue {
  println("This was true")
}
```

Only `Bool` values may be used for the condition, and `0` and `1` are not equivalent to booleans. You must be explicit in checking values.

```Swift
if numberValue == 1 {
  println("The value was 1.")
}
```

### Switch

`switch` is much safer than in other languages. They don't fall through, so there no need for `break`. They must be exhaustive, either handling all values, or including `default`.

```Swift
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

```Swift
for user in arrayOfUsers {
  println(user)
}
```

With a dictionary:

```Swift
for (key, value) in dictionary {
  println("\(key): \(value)")
}
```

With a range:

```Swift
for index in 1...10 {
  println("Index: \(index)")
}
```

Notice the three periods, `...`. That will cover 1 to 10, a *closed range.* To omit the last value (in this case, 1 to 9), use `..<`, a *half range*.

There are also the traditional `for`, `while`, and `do/while` loops:

```Swift
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

`nil` represents the absence of a value. *optionals* are a special type that may contain `nil`, or some value. To access that underlying value, you *unwrap* the optional.

Declare an optional with `?`. Unwrap it with `!`.

```Swift
let optionalValue : Int? = 1
if optionalValue != nil {
  let intValue = optionalValue!
}
```

`nil` is not a boolean. You must check `optionalValue != nil`. However, there's shorthand:

```Swift
var optionalValue: Int? = 1
if let optionalValue = optionalValue {
  println("The int was \(optionalValue)")
} else {
  println("The int was not there.")
}
```

## Functions

```Swift
func functionName(){
    println(“Hello World”)
}

functionName() // "Hello World"
```

With parameters:

```Swift
func functionName(variableName: String){
    println(“Hello \(variableName)”)
}

functionName("Ben") // "Hello Ben"
```

With return values:

```Swift
`func greetingGenerator(name: String) -> String {`
  `return "Hello \(name)"`
`}`

let greeting = greetingGenerator("World")
println(greeting) // "Hello World"
```

With default Values:

```Swift
func functionName(name: String = "Somebody"){
    println("Hello \(name)!")
}
functionName() // "Hello Somebody"
```

For clarity, use keyword parameters:

```Swift
func performGreeting(greeting:String, withName name: String){
    println("\(greeting) \(name).")
}
performGreeting("Hello", withName:"Ben")
```

To use the same keyword name as the variable name:

```Swift
func performGreeting(greeting:String, #name: String){
    println("\(greeting) \(name).")
}

performGreeting("Hello", name:"Ben")
```

### Closures

Functions are just named closures.

```Swift
var greetingClosure: (String, String) -> (String) = {
    (greeting, name) in
    return "\(greeting) \(name)."
}

greetingClosure("Hello", "Ben")

```

## Classes

```Swift
class Animal {

}
var myAnimal = Animal()
```

### Subclassing

```Swift
class Dog: Animal {
    
}

```

### Methods

```Swift
class Dog: Animal {
  func bark() -> String {
    return "Woof"
  }
}

let myDog = Dog()
myDog.bark()
```

You must use `override` to override a method.

```Swift
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

```Swift
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

To add behavior normally contained in a getter or setter, use property observers:

```Swift
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

`newValue` and `oldValue` are variables available within property observers.

For properties without ivars, use computed getters and setters.

```Swift
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

If the instance is declared with `let`, the object's properties are still mutable. The constant just can't point to another object.

```Swift
let myDog = Dog()
myDog.cute = true // Valid
myDog = Dog()     // Invalid
```

### Protocols

```Swift
protocol Domesticated {
    var name: String? { get set }
    func respondToName() -> ()
}

class Dog: Animal, Domesticated {
    var name: String?
    func respondToName() {
        println("Wag tail")
    }
}
```

### Initializers and Deinitializers

The initializer must make sure every stored property has a value before any methods are called, including `super.init()`

```Swift
class Dog: Animal {
  var cute: Bool
  override init() {
    cute = true
    super.init()
  }
}
```

This is equivalent to the default initializer for:

```Swift
class Dog: Animal {
  var cute = true
}
```

By overriding `init()`, you lose the default assignment behavior for all properties.

To perform cleanup code before an object is destroyed:

```Swift
class Dog: Animal {
    deinit {
       println("Cleaned up")
    }
}
```

## Structs

Swift structs are like C structs, but much more powerful, resembling classes. These advanced features are covered in [[Intermediate Swift|Swift Intermediate]].

```Swift
struct User {
    var name: String
    var occupation: String
}
```

By default, structs come with a member initializer.

```Swift
let ben = User(name: "Ben Sandofsky", occupation:"Engineer")
```

As with Objective-C, structs are passed by value, classes are passed by reference.

Unlike a class, when a struct is declared with `let`, all of its properties are immutable.

## Enum

Like structs, Swift enums are more powerful than their C equivalents. See [[Intermediate Swift|Swift Intermediate]].

```Swift
enum Color: Int {
  case Red = 1, Orange, Yellow, Green, Blue, Indigo, Violet
}
let orangeValue = Color.Orange
```

To access the underlying value, use `toRaw()`:

```Swift
println("Orange raw value: \(orangeValue.toRaw()).")
```

Enums may use other underlying values:

```Swift
enum ControlCharacters: Character {
  case Tab = "\t"
  case Linefeed = "\n"
  case CarriageReturn = "\r"
}
```

They can have no raw value:

```Swift
enum Season {
  case Spring, Summer, Fall, Winter
}
```

If the enum type can be inferred, you can omit it.

```Swift
let label = UILabel
label.textAlignment = .Right
```

## Extensions

You may extend classes, structs, and enums, without touching the original source code. It is similar to a category in Objective-C, or monkey patching in Ruby.

```Swift
extension String {
    func tweetable() -> Bool {
        return countElements(self) <= 140
    }
}
```