## Overview

A framework is bundle of code letting you add functionality to your app. Apple's frameworks let you add features like maps and the user's location. Other developers' frameworks like Parse let you easily syncronize data to the cloud. 

This page covers the basics of adding Apple's iOS frameworks to your project. By the end of it you will understand how expose frameworks' methods and classes in you project. 

## Adding Frameworks to Project

### In Brief

![Adding Framework gif](http://i.imgur.com/goEWO6l.gif)

Click on your project, select your app target, press general, then add the framework you need. For example, MapKit.framework

### In Depth

Click on your project, select your app target, press Build Phases. Here are the steps the compilier takes to build your app. 

In "Link Binary With Libraries," pressing the plus button allows you to add a framework. 

Note: If you forget to link a library, your app will often compile correctly, but crash at runtime. 

## Using a linked Framework

### import

In every swift file you need to access the framework's methods and classes, you'll need to `import` the framework. For example, to use MKMapView in your MapViewController, `import MapKit`.

```Swift
import UIKit
import MapKit

class MapViewController: UIViewController{
    var mapView: MKMapView!
    …
```

# Further Reading

* [[Project Basics]]