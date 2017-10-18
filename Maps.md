## Overview
This guide is an introduction to using MapKit to set up a map view, plot locations, and draw overlays on it in your app.

## Map View Set Up
Drag a Map Kit View from the Object Library onto your storyboard:
![Place Map Kit View](https://i.imgur.com/8CAY8rt.gif)

Add MapKit framework to your view controller:
```swift
import MapKit
```

Create an outlet:  
Control-drag the map view in the storyboard inside your view controller definition.
Release and name the outlet: mapView
![Create Map Kit View outlet](https://i.imgur.com/0zspz18.gif)

You should now see a map when you build and run your project!

## Set Up Initial Properties
You probably want to see a particular area of the world in your app rather than the entire world. So let’s set up so initial properties of the map.

For example, let’s center the map in San Francisco (37.7833, -122.4167).
There are two things you need to center the map at a location:
1. The coordinates  
2. A region to display to get the correct zoom level of the map. Create a MKCoordinateSpan that defines the area spanned by the region you want to display. One degree of latitude is approximately 111 km or 69 miles.

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    // set the region to display, this also sets a correct zoom level
    // set starting center location in San Francisco
    let centerLocation = CLLocation(latitude: 37.7833, longitude: -122.4167)
    goToLocation(centerLocation)
}

func goToLocation(location: CLLocation) {
    let span = MKCoordinateSpanMake(0.1, 0.1)
    let region = MKCoordinateRegionMake(location.coordinate, span)
    mapView.setRegion(region, animated: false)
}
```

Build and run your app again. You should see your map centered in San Francisco (or your desired location).

MKMapView already supports standard interactions like scrolling to a different part of the map and adjusting the zoom level. These interactions are enabled by default, but you can disable them by accessing the properties in the map view: zoomEnabled, scrollEnabled, pitchEnabled, rotateEnabled

## Display and Navigate to User's location
You can also center the map at the user's current location.
First, request permission to use the user's location:  

![Request permission](https://i.imgur.com/Vgq2ecI.png)

Import CoreLocation framework

```swift
import CoreLocation
```

Add a locationManager:

```swift
class MapViewController: UIViewController, CLLocationManagerDelegate {
  @IBOutlet weak var mapView: MKMapView!
  var locationManager : CLLocationManager!

  override func viewDidLoad() {
    super.viewDidLoad()
    ...
    locationManager = CLLocationManager()
    locationManager.delegate = self
    locationManager.desiredAccuracy = kCLLocationAccuracyNearestTenMeters
    locationManager.distanceFilter = 200
    locationManager.requestWhenInUseAuthorization()
  }
}
```

Edit the Info.plist to tell users why we're requesting their location:   
Click on Info.plist and add a new row with key: NSLocationWhenInUseUsageDescription and value: To show your location on the map
![Info.plist](https://i.imgur.com/2hz5Mok.png)

To show the user's location on the map:
Click on your map view and then check Shows User Location in the Attributes Inspector  
![Show User Location](https://i.imgur.com/CAChYxf.png)

Go to the user's location when permission has been given:

```swift
func locationManager(_ manager: CLLocationManager, didChangeAuthorization status: CLAuthorizationStatus) {
    if status == CLAuthorizationStatus.authorizedWhenInUse {
        locationManager.startUpdatingLocation()
    }
}

func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
    if let location = locations.first {
        let span = MKCoordinateSpanMake(0.1, 0.1)
        let region = MKCoordinateRegionMake(location.coordinate, span)
        mapView.setRegion(region, animated: false)
    }
}
```

### Set current location in iPhone simulator
In the simulator, select Debug > Location

![Apple](https://i.imgur.com/ktDU1oI.png)  
*Location with Apple selected*

## Add Map Annotations
MKAnnotation is used for annotations in a map. It contains the coordinate of the annotation only. A MKAnnotationView is used to display an annotation.
We'll go over different ways to display an annotation:
### MKPointAnnotation
MKPointAnnotation is a simple annotation and supports a title.

![MKPointAnnotation](https://i.imgur.com/7tskBjZ.png)

```swift
// add an Annotation with a coordinate: CLLocationCoordinate2D
func addAnnotationAtCoordinate(coordinate: CLLocationCoordinate2D) {
    let annotation = MKPointAnnotation()
    annotation.coordinate = coordinate
    annotation.title = "An annotation!"
    mapView.addAnnotation(annotation)
}

// add an annotation with an address: String
func addAnnotationAtAddress(address: String, title: String) {
    let geocoder = CLGeocoder()
    geocoder.geocodeAddressString(address) { (placemarks, error) in
        if let placemarks = placemarks {
            if placemarks.count != 0 {
                let coordinate = placemarks.first!.location!
                let annotation = MKPointAnnotation()
                annotation.coordinate = coordinate.coordinate
                annotation.title = title
                self.mapView.addAnnotation(annotation)
            }
        }
    }
}
```

### MKAnnotationView
MKAnnotationView is used to create an annotation with a custom image.
We'll need MKMapViewDelegate
```swift
class MapViewController: UIViewController, MKMapViewDelegate {
  ...
}
```
Also implement the following in the view controller:
```swift
func mapView(mapView: MKMapView, viewForAnnotation annotation: MKAnnotation) -> MKAnnotationView? {
    let identifier = "customAnnotationView"

    // custom image annotation
    var annotationView = mapView.dequeueReusableAnnotationViewWithIdentifier(identifier)
    if (annotationView == nil) {
        annotationView = MKAnnotationView(annotation: annotation, reuseIdentifier: identifier)
    }
    else {
        annotationView!.annotation = annotation
    }
    annotationView!.image = UIImage(named: "customAnnotationImage")

    return annotationView
}
```
![MKAnnotationView](https://i.imgur.com/J4MJYFm.png)

### MKPinAnnotationView
MKPinAnnotationView is a subclass of MKAnnotationView. It uses the pin graphic as the annotation image, and you can set the pin color and drop animation.
```swift
func mapView(mapView: MKMapView, viewForAnnotation annotation: MKAnnotation) -> MKAnnotationView? {
    let identifier = "customAnnotationView"
    // custom pin annotation
    var annotationView = mapView.dequeueReusableAnnotationViewWithIdentifier(identifier) as? MKPinAnnotationView
    if (annotationView == nil) {
        annotationView = MKPinAnnotationView(annotation: annotation, reuseIdentifier: identifier)
    }
    else {
        annotationView!.annotation = annotation
    }
    annotationView!.pinTintColor = UIColor.greenColor()

    return annotationView
}
```
![MKPinAnnotationView](https://i.imgur.com/CAaXABK.png)

## Draw Map Overlays
Here's an example of a circular overlay centered at a coordinate.
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    ...
    mapView.delegate = self
    // draw circular overlay centered in San Francisco
    let coordinate = CLLocationCoordinate2D(latitude: 37.7833, longitude: -122.4167)
    let circleOverlay: MKCircle = MKCircle(centerCoordinate: coordinate, radius: 1000)
    mapView.addOverlay(circleOverlay)
}
```
Implement the following in the view controller:
```swift
func mapView(mapView: MKMapView, rendererForOverlay overlay: MKOverlay) -> MKOverlayRenderer {
    let circleView = MKCircleRenderer(overlay: overlay)
    circleView.strokeColor = UIColor.redColor()
    circleView.lineWidth = 1
    return circleView
}
```
![Map Overlay](https://i.imgur.com/lZUFlbz.png)

## Alternative Options
In addition to MapKit, you can integrate other map SDKs in your app.
One reason to use alternate maps is for cross platform consistency.

### Google Maps
* [Google Maps SDK for iOS](https://developers.google.com/maps/documentation/ios-sdk/?hl=en)
* The Google Places SDK is also packaged in Google Maps SDK, useful for autocomplete searching.

### Mapbox
* [Mapbox iOS SDK](https://www.mapbox.com/mapbox-ios-sdk-legacy/)
* Mapbox is a set of open source tools used to create custom maps.

### OpenStreetMap
* [OpenStreetMap API](http://wiki.openstreetmap.org/wiki/API)
* OpenStreetMap is a project that creates and distributes data of the world.
* OpenStreetMap API allows reading and editing raw data, and does not provide a way to embed a map (like in MapKit or Google Maps).
* OpenStreetMap has a lot of [map features](http://wiki.openstreetmap.org/wiki/Map_Features) like buildings, highways, shops...

## References
* [Apple's Location and Maps Programming Guide](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/LocationAwarenessPG/Introduction/Introduction.html)
* [Apple's MapKit Framework Reference](https://developer.apple.com/library/mac/documentation/MapKit/Reference/MapKit_Framework_Reference/index.html)
