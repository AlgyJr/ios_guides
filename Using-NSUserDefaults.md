There are several different persistence mechanisms in iOS. The simplest to use is a persistent key-value store called NSUserDefaults. You might use NSUserDefaults for similar purposes as cookies in web development. They can store things like application settings, the current user, or a flag for whether a user has already seen a helpful hints popover.

### Step 1: Saving Data

To save a key to NSUserDefaults, do something like this:

```swift
var defaults = NSUserDefaults.standardUserDefaults()
defaults.setObject("some_string_to_save", forKey: "some_key_that_you_choose")
defaults.setInteger(123, forKey: "another_key_that_you_choose")
defaults.setBool(true, forKey: "yet_another_key_that_you_choose")
defaults.synchronize()

```

Note the `synchronize` call. NSUserDefaults automatically and periodically synchronizes, but to manually flush the keys and values to disk, call `synchronize` to guarantee that your updates are saved.

### Step 2: Loading Data

To load a key from NSUserDefaults, do something like this:

```swift
var defaults = NSUserDefaults.standardUserDefaults()
var stringValue = defaults.objectForKey("some_key_that_you_choose") as! String
var intValue = defaults.integerForKey("another_key_that_you_choose")
var boolValue = defaults.boolForKey("yet_another_key_that_you_choose")
```
