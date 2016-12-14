There are several different persistence mechanisms in iOS. The simplest to use is a persistent key-value store called UserDefaults. You might use UserDefaults for similar purposes as cookies in web development. They can store things like application settings, the current user, or a flag for whether a user has already seen a helpful hints popover.

### Step 1: Saving Data

There are many data types that can be stored in UserDefaults including: `array`, `bool`, `data`, `dictionary`, `float`, `integer`, `object`, `stringArray`, `string`, `double` and `url`. Checkout the [UserDefaults API Reference](https://developer.apple.com/reference/foundation/userdefaults) for more info.

To save a key to UserDefaults, do something like this:

```swift
//Access UserDefaults
let defaults = UserDefaults.standard

// Set a String value for some key.
defaults.set("Hello World!", forKey: "myString")
// Set an Integer value for some key.
defaults.set(123, forKey: "myInt")
// Set a Double value for some key.
defaults.set(123.00, forKey: "myDouble")
// Set a Bool value for some key.
defaults.set(true, forKey: "myBool")

// Force UserDefaults to save.
defaults.synchronize()

```

Note the `synchronize` call. UserDefaults automatically and periodically synchronizes, but to manually flush the keys and values to disk, call `synchronize` to guarantee that your updates are saved.

### Step 2: Loading Data

To load a key from UserDefaults, do something like this:

```swift
// Access UserDefaults
let defaults = UserDefaults.standard
```

Get values of different types from a given key
```swift
// Get a string value and provide a default string in the case the string is nil.
let stringValue = defaults.string(forKey: "myString") ?? "my default string"
// Get an Integer value.
let intValue = defaults.integer(forKey: "myInt")
// Get a Double value.
let doubleValue = defaults.double(forKey: "myDouble")
// Get a Bool value.
let boolValue = defaults.bool(forKey: "myBool")
```
