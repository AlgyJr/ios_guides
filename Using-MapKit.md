# Overview

MapKit lets you use native maps in your app and overlay custom pins and annotations, including the user's location. It is generally the best way to visualize location information your app. 

This page covers the most common topics for MapKit. By the end of it, you will understand how to accomplish the following:
* Center map over a point
* Drop pins at locations
* Use custom images for map pins – aka "custom annotations"
* Detect pin taps
* Show the user their location
* Send the user to get directions to pins

![Custom MapKit annotations gif](http://i.imgur.com/WIwqNtn.gif)
*A MapKit map with custom images on pin annotations*

# Setup

You will need to add `MapKit.framework` to your project first. You can find out how by reading our guide on [[Project Frameworks]].

Be sure you `import MapKit` into whatever view controllers will need to use maps. 

# Using MapKit
## Add a `MKMapView` map to your application 

* You can drag a `MapKit View` from Interface Builder's Object Library onto your Storyboard, and configure it just like a regular view. 

![MapKit View's configuration options](http://i.imgur.com/MjOMcsu.png)

Here are the configuration options for `MKMapView` in the "DJ tab" (shown above):
1. Type – Choose from Satellite vs Standard vs Hybrid Map types
2. Allows – Disable Zooming, Rotating, and Scrolling to prevent the Map from moving, while still allowing interaction with any pins present
3. Shows, Buildings and Annotations – toggle to let Apple's default points of interest show when available 
4. Shows, User Location – Show's user their location, when they're on Map. **Using this first requires the user's permission for their location, by getting it through the `CoreLocation.framework`**

* *99.8% of the time* after creating adding a Map View in interface builder, you'll want to create an outlet for it in your view controller. 

```swift 
import UIKit
import MapKit

class MapViewController: UIViewController{
    var mapView: MKMapView!

    ...
```

## Centering a `MKMapView` over a point, with a specific region

![Milestone 2 gif](http://i.imgur.com/tro9qJv.gif)

You can set the Map's region whenever you want. In `viewDidLoad` use `animated: false`, if the map is on screen, you should use `animated: true`. 

```swift 
override func viewDidLoad() {
    super.viewDidLoad()

    mapView.setRegion(MKCoordinateRegionMake(CLLocationCoordinate2DMake(37.783333, -122.416667), MKCoordinateSpanMake(0.1, 0.1)), animated: false) 
    //one degree of latitude is approximately 111 kilometers (69 miles) at all times.
}
```

## Drop pins at locations

Pins are the result of adding `MKPointAnnotation`s to your view.

```swift 
func addPin() {
    let annotation = MKPointAnnotation()
    var locationCoordinate = CLLocationCoordinate2DMake(37.783333, -122.416667)
    annotation.coordinate = locationCoordinate
    annotation.title = "Picture!"
    mapView.addAnnotation(annotation)
}
```

When you add a `annotation.title`, by default the system will display a "callout" when tapped. You can find out how to disable `canShowCallout` in *Use custom images for map pins*.

## Detect pin taps

You can trigger any action when a user taps on your pin. 

* First you will need to become the delegate of `mapView`.
``` swift 
override func viewDidLoad() {
    super.viewDidLoad()
    mapView.delegate = self
}
```
* Then you'll need to add a function, letting you know that your annotation was tapped. 
```swift 
func mapView(mapView: MKMapView!, didSelectAnnotationView view: MKAnnotationView!) {
    UIAlertView(title: "tapped pin!", message: view.annotation.title, delegate: nil, cancelButtonTitle: "OK").show()
}
```

## Use custom images for map pins 

* Set `mapView.delegate = self` as you done in *Detect pin taps*.
* Implement the following function in your view controller, `annotationView.image = thumbnailImage` is your time to shine by choosing the right thumbnailImage

```swift
func mapView(mapView: MKMapView!, viewForAnnotation annotation: MKAnnotation!) -> MKAnnotationView! {
    let reuseID = "myAnnotationView"
    var annotationView = mapView.dequeueReusableAnnotationViewWithIdentifier(reuseID)
    if (annotationView == nil) {
        annotationView = MKPinAnnotationView(annotation: annotation, reuseIdentifier: reuseID)
        annotationView.canShowCallout = true //set false to disable MKMapView's automatic pop-up with title
    }
    
    var thumbnailImage = getOurThumbnailForAnnotation(annotation)
    annotationView.image = thumbnailImage 
 
    return annotationView
}
```

Note: The `annotationView`'s image will be displayed at full-size. No resizing is possible on the Map itself. For image resizing, see the FAQ at the bottom of this page.

### One approach to knowing which thumbnailImage to use

```swift
var imageAnnotationDictionary : Dictionary = {}

func getOurThumbnailForAnnotation(annotation : MKAnnotation){

}

func addPinWithImage(image: UIImage) {
    let annotation = MKPointAnnotation()
    var locationCoordinate = CLLocationCoordinate2DMake(37.783333, -122.416667)
    annotation.coordinate = locationCoordinate
    mapView.addAnnotation(annotation)
}
```

## Send the user to get directions to pins

## Show the user their location

In order to show the user their location, first we will need to request permission to use their location. 

* `import CoreLocation` the `CoreLocation.framework`, as referenced by [[Project Frameworks]].
**Ask the user for permission to use their location.**

# FAQ
## Q: How do you know what coordinates to use? 

A: As with many things, Google is the answer– [Coordinates of San Francisco](https://www.google.com/search?q=coordinates+of+san+francisco)
* If the first number is South, use the negative `Latitude` in  `CLLocationCoordinate2DMake(latitude: -1.0 * Google's Latitude , longitude: Google's Longitude)`
* If the second number is West, use a negative `Longitude` in  `CLLocationCoordinate2DMake(latitude: Google's Latitude , longitude: -1.0 * Google's Longitude)`

You can also use this [coordinate conversion service](http://www.latlong.net/convert-address-to-lat-long.html).

## Q: I've got a big image, how do a make a small one for my pin annotation's image?

A: Here's a snippet for resizing a UIImage for MKAnnotationView's image property:

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

# Further Reading

TODO: **[[Foursquare API]] can help you place items on the Map**