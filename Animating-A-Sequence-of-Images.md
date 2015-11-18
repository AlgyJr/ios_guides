This guide will demonstrate one method for creating a repeating "flip book" or animated gif effect given multiple images. We will use a method, ``animatedImageWithImages()`` that can be called on a ``UIImageView`` in order to cycle through a collection of images at a duration that we will specify.

### Step 1: Add Your Image Assets  
Make sure the images for your animation are all the same size.  
- Add the image assets that you want to use as "frames" in your animation to the Assets.xcassets folder. [adding Image Assets](https://github.com/codepath/ios_guides/wiki/Adding-Image-Assets)  
![frame 1](http://i.imgur.com/SnGmQAi.png) ![frame 2](http://i.imgur.com/Pzw1BNR.png) ![frame 3](http://i.imgur.com/NTfz9I7.png)

### Step 2: Add an ImageView
We will hold our image animation in a UIImageView. It will need to be the same size as our image "frames".  
- Add a UIImageView to your ViewController. You can do this in Storyboard or programmatically. [Using UIImageView](http://courses.codepath.com/courses/ios_for_designers/pages/using_uiimageview)  
HINT: A simple way is to just drag the first image "frame" on to the Storyboard from the *Media Library* at the bottom of the *Utilities* pane and it will automatically create an ImageView the correct size of your image frame. 
- If you created your ImageView in Storyboard, create an outlet to your ViewController code file by ``ctr + drag`` from the ImageView.
  
### Step 3: Define Your Instance Variables  
Instance Variables are defined below the ``class ViewController: UIViewController { `` and above the ``override func viewDidLoad() {``
- Define instance variables for your images.

```Swift
var loading_1: UIImage!
var loading_2: UIImage!
var loading_3: UIImage!
```  

We will need to store our images an array in order to easily reference them for our animation.  
- Define a class variable for your ``images`` array. 
- Note: ``images`` is plural because it will contain multiple images. ``[UIImage]!`` means it will contain a collection of items, each item being of type, UIImage.

```Swift
var images: [UIImage]!
```

We will store our animated image into another variable of type UIImage.  
- Define a class variable for your animated image.

```Swift
var animatedImage: UIImage!
```

### Step 4: Assign Values to Your Variables
It is good practice to define your class variables with an object type, and then assign them values within the ``viewDidLoad`` method.  
- Link the actual image files in your assets to your image variables. ``myImage = UIImage(named: "image file name)"``

```Swift
loading_1 = UIImage(named: "loading-1")
loading_2 = UIImage(named: "loading-2")
loading_3 = UIImage(named: "loading-3")
```

- Put your images into your ``images`` array.

```Swift
images = [loading_1, loading_2, loading_3]
```

- Call the ``animatedImageWithImages`` method and store it in ``animatedImage`` This method takes two arguments
   - The array of images you want it to cycle through
   - The duration you want it to cycle at in seconds. Meaning it will go through all the frames in that time.

```Swift
animatedImage = UIImage.animatedImageWithImages(images, duration: 1.0)
```

- Set the image inside your ImageView to be, ``animatedImage``.

```Swift
ImageView.image = animatedImage
```

### Step 5: Run Your App!  
![animated image example](http://i.imgur.com/SjDREPj.gif)