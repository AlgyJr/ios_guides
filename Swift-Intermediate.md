# Introduction

This guide covers the more elegant features of the language. It is recommended for after competency in the [[basic rules and syntax|Swift Basics]].

This guide is designed to be concise. For in-depth details, check out the official documentation:

* [WWDC 2014: Intermediate Swift](https://developer.apple.com/videos/wwdc/2014/#403)
* [iBooks: The Swift Programming Language](https://itunes.apple.com/us/book/swift-programming-language/id881256329)

## Types and Casting

`AnyObject` is an ambiguous type that can represent any object. An even more ambiguous type, `Any`, covers non-class objects such as Structs and Enums.

```swift
var myArray: [AnyObject] = Array()
myArray.append(Dog()) // Legal
myArray.append(Homework.inProgress)   // Illegal. An enum is not a class instance
```

While strong typing is preferred, sometimes it's unavoidable. For instance, a JSON dictionary can have mixed types of values.

To check an object's type, use `is`

```swift
if userDictionary["bio"] is String {
    print("The bio is present")
} else if userDictionary["bio"] is NSNull {
    print("The bio is not available.")
}
```

Use `as` to downcast:

```swift
let bio = userDictionary["bio"] as String
```

It's safer to downcast with `as?`, which returns an optional, in case the downcast fails.

```swift
if let bio = userDictionary["bio"] as? String {
    print("Bio: \(bio)")
} else {
    print("Bio is unavailable.")
}
```

To write functions that accept multiple types without losing type information, use Generics, which are covered in [[Advanced Swift|Swift Advanced]].

## Handling Nil

### Optional Chaining

If you have a chain of properties, rather than unwrap each individually, use the `?` operator.

```swift
var myValue: Int? = nil
if object != nil && object!.childObject != nil {
  myValue = object!.childObject!.method()
}

// Equivalent
var myValue: Int? = object?.childObject?.method()
```

If any part of the chain is `nil`, it stops evaluating and returns `nil`, ignoring everything to the right.

### Implicitly Unwrapped Optionals

You can declare an optional that automatically unwraps itself, using the `!` postfix.

```swift
var i: Int? = 1
if i != nil {
    i = i! + 1
}

// Equivalent

var j: Int! = 1
if j != nil {
    j = j + 1
}
```

This is more dangerous than explicit unwrapping, but useful when dealing with values that will only briefly be `nil`.

### Nil Coalescing

A frequent pattern is, "If the optional is nil, use this other value." For brevity, use the `??` operator. The following two lines are equivalent:

```swift
a != nil ? a! : b
a ?? b
```

## Classes

### Designated Initializers

Normally, you put initialization code in your `init()` override. However, some classes have *designated initializers*, which are the official way to instance a class. For instance, on UIView, you should override `init(frame:)` to perform your initialization.

Classes can have multiple designated initializers. For instance, `init(coder:)` is a designated initializer used when an object is unarchived.

### Convenience Initializers

Designated initializers are allowed to call "up", to a super initializer. Convenience initializers are only allowed to call "across" their class, to a designated initializer.

```swift
class Dog: Animal {
  var name: String = ""
  convenience init(randomName: Bool){
    self.init()
    if randomName {
      self.name = generateRandomName()
    }
  }
}
```

### Initializer Inheritance

Swift tries to only inherit initializers when it's safe to do so, which can be confusing. For instance, you commonly override UIViewController's designated initializer to perform setup logic. If you just added this code, you would get a compilation error:

```swift
override init(nibName nibNameOrNil: String?, bundle nibBundleOrNil: Bundle?) {
    super.init(nibName: nibNameOrNil, bundle: nibBundleOrNil)
    self.edgesForExtendedLayout = [] // This used to be UIRectEdge.None
}
```

This is because by overriding this initializer, you have lost the method `init(coder:)`, which is the other designated initializer, and used when the view controller is loaded from a [[Storyboard|Interface Builder]].

The rules of initializer inheritance are:

1. If you don't add a designated initializer, you inherit all superclass designated initializers.
2. If you do add a designated initializer, you must provide all designated initializers to inherit the convenience initializers.

### Lazy Properties

Lazy properties are instanced the first time they're used.

```swift
class MailAccount {
  lazy var inbox = LoadInboxFromDisk()
}
```

### Nesting

If an enum, class, or struct is only used within a certain context, such as one particular class, it can be convenient to nest their declaration to namespace it.

```swift
class Homework {
  var status: State = .inProgress

  enum State {
    case inProgress
    case submitted
  }
}
```

## Closures

### Concise Closures

```swift
array.sort({(a: String, b: String) -> Bool in
  return a < b
})
```

The types can be removed thanks to type inference:

```swift
array.sort({ a, b in
  return a < b
})
```

This pattern is so common, one line closures have an implied `return` statement:

```swift
array.sort({ a, b in a < b })
```

This can be edited further with implicit argument names:

```swift
array.sort({ $0 < $1 })
```

Finally, If the last parameter to a method is a closure, it can be written outside the parenthesis, as a *trailing closure*.

```swift
array.sort { $0 < $1 }
```

## Structs

Structs may have methods, computed properties, initializers, and they can adhere to protocols.

However, structs are not classes. There is no subclassing. They are passed by value, while classes are passed by reference. While they have an initializer, there is no deinitializer.

Other than for the need for inheritance, if two object instances are identical and interchangeable, use a struct, otherwise, use a Class. 

```swift
struct Rect {
  var origin: Point
  var size: Size
  var area: Double {
     return size.width * size.height
  }
}
```

To obey the rules of `var` vs `let`, mutating methods must use the `mutating` keyword:

```swift
struct Person {
  var age: Int
  mutating func growOlder(){
    age += 1
  }
}
```

## Enums

Enums may also have methods and conform to protocols.

```swift
enum HomeworkState {
  case inProgress
  case submitted
  init(){
    self = .inProgress
  }
  func description() -> String {
    switch self {
      case .inProgress:
        return "In Progress"
      case .submitted:
      return "Submitted"
    }
  }
}

let state = HomeworkState()
print(state.description()) // "In Progress"
```

### Associated Values

Enums can store associated values:

```swift
enum HomeworkState {
  case inProgress
  case submitted
  case graded(Int)
}

let homework = HomeworkState.graded(100)
```

## Extending Basic Types

Like classes, you can extend structs, enums, and even primitives like `Int`

```swift
extension Int {
    func even() -> Bool {
        return self % 2 == 0
    }
}
2.even() // true
```

## Pattern Matching

Pattern matching allows a concise way to filter values, and bind those values to constants.

```swift
switch statusCode {
  case 200...299:
    print("Success")
  default:
    print("Error")
}
```

They can also match on variations of tuples:

```swift
let point = (0, 10)

switch point {
  case (0, 0):
    print("Origin")
  case (let x, 0):
    print("X-Axis:\(x)")
  case (0, let y):
    print("Y-Axis: \(y)")
  case (let x, let y):
    print("Off axis: \(x), \(y)")
}
// "Y-Axis: 10"
```

They can be applied to enum associated values:

```swift
switch homeworkStatus {
  case .inProgress:
    print("In progress")
  case .submitted:
    print("Submitted")
  case .graded(60 ..< 70):
    print("Received a D")
  case .graded(70 ..< 80):
    print("Received a C")
  case .graded(80 ..< 90):
    print("Received a B")
  case .graded(90 ... 100):
    print("Aced it!")
  case .graded(let grade):
    print("Failed, with a \(grade)")
}
```

It is possible to match on type using `is`, or match and bind using `as`.

```swift
func makeAnimalDoSomething(animal: Animal){
  switch animal {
    case let dog as Dog:
      dog.wagTail()
    case let bird as Bird:
      bird.flapWings()
    default:
      animal.eat()
  }
}
```

## Memory Management

Like Objective-C, Swift uses reference counting for memory management, built on [ARC](http://en.wikipedia.org/wiki/Automatic_Reference_Counting).

As such, you must use *weak* references to avoid reference cycles.

```swift
class MailFolder {
  weak var sendingAccount: Account?
}
```

For references up the object graph, it can be more convenient to use `unowned` references, which act like weak implicitly unwrapped optionals. However, only use `unowned` references when you're sure the parent will exist for the lifetime of the child.

```swift
class Parent {
  var child: Child!
  init(){
    child = Child(parent: self)
    child.parent = self
  }
}

class Child {
  unowned var parent: Parent
  init(parent: Parent){
    self.parent = parent
  }
}
```