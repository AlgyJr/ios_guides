## Location

### Step 1: Add the Location Framework

In build phases, add the location framework.

### Step 2: Identifying the desired location permission

So the first thing you need to do is to add one or both of the following keys to your Info.plist file:

- NSLocationWhenInUseUsageDescription
- NSLocationAlwaysUsageDescription

### Step 3: Create the location manager

In the app delegate,

```swift
import CoreLocation

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?
    var locationManager: CLLocationManager! = CLLocationManager()

```

### Step 4: Request permission

```swift
locationManager.requestAlwaysAuthorization()
```

### Step 5: Start Updating Location

```swift
locationManager.startUpdatingLocation()
```

### Step 6: Implement Location Delegate

```swift
func locationManager(manager: CLLocationManager!, didUpdateLocations locations: [AnyObject]!) {
    var location = locations[0] as CLLocation
    
    println(location)
}
```

