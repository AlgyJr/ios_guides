## Overview

MapKit lets you use native maps in your app and overlay custom Annotations, a.k.a. Pins, including the user's location. 

This page covers some of the most common tasks requiring MapKit, by the end of it you should be equipped for the majority of MapKit use cases. 

![Custom MapKit annotations gif](http://i.imgur.com/WIwqNtn.gif)

### Necessary knowledge

* Basic level in XCode
* Basic level in Storyboards/ Interface Builder

## Setup

You will need to add `MapKit.framework` to your project first. You can find out how by reading our guide on [[Project Frameworks]].

Be sure you `import MapKit` into whatever view controllers will need to use maps. 

## Using MapKit

### Add a `MKMapView` in Interface Builder 

* You can drag a `MapKit View` from Interface Builder's Object Library onto your Storyboard Scene, and configure it just like a regular view. 

![MapKit View's configuration options](http://i.imgur.com/MjOMcsu.png)

Here are the configuration options for `MKMapView` in the "DJ tab" shown above:

Type lets you choose from Satellite vs Standard vs Hybrid Map types. Allows lets you prevent the Map from moving in different ways.

Shows lets you show buildings and Apple's points of interest on-map, and also lets you toggle displaying the user their location as an Annotation when while on-map (See *Show the user their location on-map*, below).

* Roughly *100% of the time* after creating adding a Map View in interface builder, you'll want to create an outlet for it in your view controller. 

```swift 
import UIKit
import MapKit

class MapViewController: UIViewController{
    @IBOutlet weak var mapView: MKMapView!

    ...
```

* Then *90% percent of the time* you'll want to set your `mapView.delegate = self`. 

``` swift 
override func viewDidLoad() {
    super.viewDidLoad()
    mapView.delegate = self
}
```

* You'll also need to add `MKMapViewDelegate` to your View Controller's protocols 

```swift 
class MapViewController: UIViewController, MKMapViewDelegate {
    ...
```

### Centering a `MKMapView` at a point, with a displayed region

You can set the Map's region whenever you want. 

```swift 
override func viewDidLoad() {
    super.viewDidLoad()
    ...
    
    // One degree of latitude is approximately 111 kilometers (69 miles) at all times.
    // San Francisco Lat, Long = latitude: 37.783333, longitude: -122.416667
    let mapCenter = CLLocationCoordinate2D(latitude: 37.783333, longitude: -122.416667)
    let mapSpan = MKCoordinateSpan(latitudeDelta: 0.1, longitudeDelta: 0.1)
    let region = MKCoordinateRegion(center: mapCenter, span: mapSpan)
    // Set animated property to true to animate the transition to the region
    mapView.setRegion(region, animated: false)
}
```

One degree of latitude is approximately 111 kilometers (69 miles). 

In general use `animated: false` if the map isn't on screen, and `animated: true` if the map is already displayed. *Want different coordinates? See the FAQ.*

### Drop Pins at locations

When you add a `MKPointAnnotation` to your view, a Pin appears!

```swift 
func addPin() {
    let annotation = MKPointAnnotation()
    let locationCoordinate = CLLocationCoordinate2D(latitude: 37.779560, longitude: -122.393027)
    annotation.coordinate = locationCoordinate
    annotation.title = "Founders Den"
    mapView.addAnnotation(annotation)
}
```

### Detect Annotation/Pin taps

You can trigger any action when a user taps on your Annotation. 

* Set your `mapView.delegate = self`
* Add this function, letting you know that your Annotation was tapped

```swift 
func mapView(_ mapView: MKMapView, didSelect view: MKAnnotationView) {
    if let annotation = view.annotation {
        if let title = annotation.title! {
            print("Tapped \(title) pin")
        }
    }
}
```

### Use custom images for map Annotations

* Set your `mapView.delegate = self`
* Implement the following function in your view controller

```swift
func mapView(_ mapView: MKMapView, viewFor annotation: MKAnnotation) -> MKAnnotationView? {
    let reuseID = "myAnnotationView"
    var annotationView = mapView.dequeueReusableAnnotationView(withIdentifier: reuseID)
    if annotationView == nil {
        annotationView = MKAnnotationView(annotation: annotation, reuseIdentifier: reuseID)
        /// show the callout "bubble" when annotation view is selected
        annotationView?.canShowCallout = true
    }

    /// Set the "pin" image of the annotation view
    let pinImage = UIImage(named: "pin")
    annotationView?.image = pinImage

    /// Add an info button to the callout "bubble" of the annotation view
    let rightCalloutButton = UIButton(type: .detailDisclosure)
    annotationView?.rightCalloutAccessoryView = rightCalloutButton

    /// Add image to the callout "bubble" of the annotation view
    let image = UIImage(named: "founders_den")
    let leftCalloutImageView = UIImageView(image: image)
    annotationView?.leftCalloutAccessoryView = leftCalloutImageView

    return annotationView
}
```

The `annotationView.image`'s image will be displayed at full-size. No resizing is possible on the Map itself. **For image resizing, see the FAQ at the bottom of this page.**

To have a different image for each Annotation, you will need to store which image belongs to which Annotation. **For sample code, see FAQ at the bottom of this page.**

### Send the user to Apple Maps with Annotation

The following example will take a tapped Annotation's coordinate, and use it to open Apple Maps where they can get directions, etc.

```swift
func mapView(_ mapView: MKMapView, didDeselect view: MKAnnotationView) {
    let lattitude = view.annotation?.coordinate.latitude
    let longitude = view.annotation?.coordinate.longitude
    guard let appleMapsURL = URL(string: "http://maps.apple.com/?q=\(lattitude),\(longitude)") else { return }
    UIApplication.shared.open(appleMapsURL, options: [:], completionHandler: nil)
}

```

### Get permission to use the user's location

In order to show the user their location, first we will need to request permission to use their location. 

* `import CoreLocation` after adding `CoreLocation.framework` to your project. [Guide: How do I add a framework?](https://github.com/codepath/ios_guides/wiki/Project-Frameworks)
* Add a `var locationManager : CLLocationManager!`, then `locationManager.requestWhenInUseAuthorization()`. It's best to do this in `viewDidLoad`. 

Your updated view controller will look something like this:

```swift 
import UIKit
import MapKit
import CoreLocation

class MapViewController: UIViewController{
    @IBOutlet weak var mapView: MKMapView!
    var locationManager : CLLocationManager!
  
    override func viewDidLoad() {
        super.viewDidLoad()
        
        locationManager = CLLocationManager()
        locationManager.requestWhenInUseAuthorization()
        ...
    ...
```

Finally, we'll need to add a line to a file called `Info.plist`

* Open `Info.plist` in your project's "Supporting Files" folder. 
* To add a new row, click on the last row, then press the (+) button that appears. 
* Paste-in the string `NSLocationWhenInUseUsageDescription` in the *Key* column. 
* Type-in some message into the *Value* column. E.g., `We'd like to show your location on-map!` 

Now when you run your project, you should see a pop-up to request permission for the user's location!

![Requesting location permission gif](http://i.imgur.com/lTThTzk.gif)

### Show the user their location on-map

* First you'll need to follow the above how-to, *Get permission to use the user's location*. 
* Then you can go into Storyboard or Interface builder and check `Shows: User Location`.

Now when the user is within the map's visible region, they will appear as a blue dot Annotation.

**NOTE: If you are using custom images for Annotations, add the following to the top of your `mapView:viewForAnnotation:` to keep the default user location Annotation**

```swift
func mapView(mapView: MKMapView!, viewForAnnotation annotation: MKAnnotation!) -> MKAnnotationView! {
    if annotation.isKindOfClass(MKUserLocation) {
        return nil
    }
    ...
}
```

###Center the map at a user's location

* First you'll need to follow the above how-to, *Show the user their location on-map*.
* Set your `mapView.delegate = self`
* Then you can add the following function to your app, which gets called every time your `MapView` updates the user's location

```swift
func mapView(mapView: MKMapView!, didUpdateUserLocation userLocation: MKUserLocation!) {
    var annotations = [mapView.userLocation]
    mapView.showAnnotations(annotations, animated: true)
}
```

### Fit the map to show both the user's location and an Annotation

* First you'll need to follow the above how-to, *Show the user their location on-map*.
* Then you can add the following function to your app, to show both the user's location Annotation and any annotation tapped

```swift 
func mapView(mapView: MKMapView!, didSelectAnnotationView view: MKAnnotationView!) {
    var annotations = [mapView.userLocation, view.annotation]
    mapView.showAnnotations(annotations, animated: true)
}
```

**Note: This example requires you to set `mapView.delegate = self`**

### Get the user's location coordinates

* First you'll need to follow the above how-to, *Get permission to use the user's location*. 
* Next, add `CLLocationManagerDelegate` to supported protocols, and variables for `lastLocation` and the `locationManager`

```swift 
class MapViewController: UIViewController, CLLocationManagerDelegate, ... {
    var locationManager : CLLocationManager!
    var lastLocation : CLLocationCoordinate2D!
    ...
}
```

* Add the following lines in `viewDidLoad`. Set `desiredAccuracy` for how accurate you'd like the user's location to be, and `distanceFilter` for how far you want the user to move before `delegate` receives a new update. 

```swift 
override func viewDidLoad() {
    super.viewDidLoad()

    locationManager = CLLocationManager()
    locationManager.delegate = self
    locationManager.desiredAccuracy = kCLLocationAccuracyNearestTenMeters
    locationManager.distanceFilter = 200
    locationManager.requestWhenInUseAuthorization()
    
    ...
}
```

* Finally, add these functions to start getting the user's location, and to save the user's location into your `lastLocation` variable when the manager updates. 

```swift
func locationManager(manager: CLLocationManager!, didChangeAuthorizationStatus status: CLAuthorizationStatus) {
    if status == CLAuthorizationStatus.AuthorizedWhenInUse {
        manager.startUpdatingLocation()
    }
}

func locationManager(manager: CLLocationManager!, didUpdateLocations locations: [AnyObject]!) {
    var location = locations.first as? CLLocation
    lastLocation = location?.coordinate
}
```

## FAQ

### How do I know what coordinates to use? 

As with many things, Google is the answer– [Coordinates of San Francisco](https://www.google.com/search?q=coordinates+of+san+francisco)
* If the first number is South, use the negative `Latitude` in  `CLLocationCoordinate2DMake(latitude: -1.0 * Google's Latitude , longitude: Google's Longitude)`
* If the second number is West, use a negative `Longitude` in  `CLLocationCoordinate2DMake(latitude: Google's Latitude , longitude: -1.0 * Google's Longitude)`

You can also use this [coordinate conversion service](http://www.latlong.net/convert-address-to-lat-long.html).

### I've got a big image, how do a make a small one for my Annotation's image?

Here's a snippet for resizing a UIImage for MKAnnotationView's image property:

```swift
var resizeRenderImageView = UIImageView(frame: CGRectMake(0, 0, 45, 45))
resizeRenderImageView.layer.borderColor = UIColor.whiteColor().CGColor
resizeRenderImageView.layer.borderWidth = 3.0
resizeRenderImageView.contentMode = UIViewContentMode.ScaleAspectFill
resizeRenderImageView.image = fullSizeImage as! UIImage

UIGraphicsBeginImageContextWithOptions(resizeRenderImageView.frame.size, false, 0.0) 
resizeRenderImageView.layer.renderInContext(UIGraphicsGetCurrentContext())
var thumbnail = UIGraphicsGetImageFromCurrentImageContext()
UIGraphicsEndImageContext()
```

We've also added a 3 pixel white border, in good taste. 

### How can I use a different image for every Annotation?

To suppliment *Use custom images for map Annotation*, here's sample code to remember which image belongs to which Annotation.

```swift
var thumbnailImageByAnnotation = [NSValue : UIImage]()

func addAnnotationWithThumbnailImage(thumbnail: UIImage) {
    let annotation = MKPointAnnotation()
    var locationCoordinate = CLLocationCoordinate2DMake(37.783333, -122.416667)
    annotation.coordinate = locationCoordinate

    thumbnailImageByAnnotation[NSValue(nonretainedObject: annotation)] = thumbnail
    mapView.addAnnotation(annotation)
}

func getOurThumbnailForAnnotation(annotation : MKAnnotation) -> UIImage?{
    return thumbnailImageByAnnotation[NSValue(nonretainedObject: annotation)]
}

func mapView(mapView: MKMapView!, viewForAnnotation annotation: MKAnnotation!) -> MKAnnotationView! {
    ...   

    annotationView.image = getOurThumbnailForAnnotation(annotation)
    return annotationView
}
```
