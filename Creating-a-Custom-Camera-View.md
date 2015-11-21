Accessing the built in [Image Picker Controller]() is a quick and easy way to get image and video capture into your app. However, when you need style and functionality that goes beyond the stock Image Picker Controller you will need to create a Custom Camera View.  
  
![Custom Camera View Demo](http://i.imgur.com/LkALEvS.gif)

### Step 1: Set Up Views In Storyboard 

![Set Up Views In Storyboard gif|200](http://i.imgur.com/KhbVi00.png) ![Outline Document gif|60](http://i.imgur.com/6CSyrY7.png)  
  
Add the following view elements to the ViewController in Storyboard:
- **UIView** This will serve as the "view finder" of your camera.
- **UIImageView** This will hold the captured still image after you take a picture.
- **UIButton** This button will "take a picture".

### Step 2: Import AVFoundation
At the top of your ViewController file, **import AVFoundation**

![Import AVFoundation](http://i.imgur.com/VhEZphd.png) 
### Step 3: Create Outlets and Actions

Create **Outlets** for the UIView and UIImageView. 
- Name the UIView, `viewFinder`.
- Name the UIImageView, `captureImageView`.

Create an **Action** for the UIButton.
- Name the method, `didTakePhoto`.




