There are several different persistence mechanisms in iOS. The simplest to use is a persistent key-value store called UserDefaults. You might use UserDefaults for similar purposes as cookies in web development. They can store things like application settings, the current user, or a flag for whether a user has already seen a helpful hints popover.

### Step 1: Saving Data

To save a key to UserDefaults, do something like this:

```swift
var defaults = UserDefaults.standard
defaults.set("some_string_to_save", forKey: "some_key_that_you_choose")
defaults.set(123, forKey: "another_key_that_you_choose")
defaults.set(true, forKey: "yet_another_key_that_you_choose")
defaults.synchronize()

```

Note the `synchronize` call. UserDefaults automatically and periodically synchronizes, but to manually flush the keys and values to disk, call `synchronize` to guarantee that your updates are saved.

### Step 2: Loading Data

To load a key from UserDefaults, do something like this:

```swift
var defaults = UserDefaults.standard
var stringValue = defaults.value("some_key_that_you_choose") as! String
var intValue = defaults.value("another_key_that_you_choose")
var boolValue = defaults.value("yet_another_key_that_you_choose")
```
