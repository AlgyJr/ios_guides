## Overview

A framework is bundle of code letting you add functionality to your app. Apple's frameworks let you add features like maps and the user's location. Other developers' frameworks like Parse let you easily syncronize data to the cloud. 

This page covers the basics of adding Apple's iOS frameworks to your project. By the end of it you will understand how use frameworks' methods and classes in your project. 

### Popular Apple frameworks 

* MapKit.framework - add maps to your app
* CoreLocation.framework - get the user's location 
* AddressBookUI.framework - used to fetch a user's contacts
* MessageUI.framework - native SMS and emails UI inside your app

## Adding Frameworks to Project

![Adding Framework gif](http://i.imgur.com/goEWO6l.gif)

Click on your project, select your app target, press general, then add the framework you need. For example, MapKit.framework

## Using a linked Framework

### import

In every swift file you need to access the framework's methods and classes, you'll need to `import` the framework. For example, to use MKMapView in your MapViewController, `import MapKit`.

```Swift
import UIKit
import MapKit
import CoreLocation
import AddressBookUI
import MessageUI

class MapViewController: UIViewController{
    var mapView: MKMapView!
    …
```

Now you can use Apple's frameworks!

# Further Reading

* [[Project Basics]]