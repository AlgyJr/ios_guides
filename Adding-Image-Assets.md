Asset Catalogs makes asset management easy. To add assets to a project, follow the steps below.

![adding image assets](https://i.imgur.com/N46BHYj.gif)

### Step 1: Select the asset catalog

As in the screenshot above, click Assets.xcassets in the project navigator to bring up the Asset Catalog for the project.

### Step 2: Add Image Set

To add an image to the project, create a new image set. Drag the png asset (jpeg won't work) from the Finder to the 1X or 2X slot. In a production app, you should include both the standard (1X) as well as retina (2X) asset. During development, you can add only one asset and XCode will automatically create the other one, although it may look blurry.

### Step 3: Using the image set

#### Example 1: Using the image set in Interface Builder

Any control that has images like UIImageViews or UIButtons can set images in Interface Builder. Any image set in the Asset Catalog will be listed in the image drop down menus.

<img src="https://i.imgur.com/hPVX8b3.gif" width="301" height="226" />

#### Example 1: Using the image set programmatically

To access images in the Asset Catalog programmatically, create UIImages using the `imageNamed:` method as shown in the snippet below.

```Objective-C
UIImage *chat = [UIImage imageNamed:@"Chat"];
UIImageView *chatImageView = [[UIImageView alloc] initWithImage:chat];
```
```Swift
var chat = UIImage(named: "Chat")
var chatImageView = UIImageView(image: chat)
```

#### Swift 3
Swift 3 introduces Image Literals. NOTE: The technique above, initializing a UIImage using its name, still works too.

![](https://i.imgur.com/5U0SFYf.gif)


### App Icon

To set the app icon, simply select "App Icon" in the asset catalog and drag a .png file into the appropriate bucket. The App Icon expects various sizes for production, although you can use just one during development. Similarly, in production, the Launch Image expects 3.5" and 4" images, but you can use just one during development.

![App icon gif](https://i.imgur.com/v3KlhOZ.gif)

### Launch Screen
For the launch image, you will need to configure the **LaunchScreen.storyboard** file as you would any other ViewController.
- Add your Launch Screen Asset to the **Assets.xcassets** folder.
- Drag the Launch Screen image from the media library (Bottom right in utilities pane), on to the LaunchScreen ViewController. 
- HINT: If Xcode is having trouble recognizing your Launch Screen image file, try renaming it in the finder before dragging it into your assets folder (example below)

![Add assets, icon, and Launch](https://i.imgur.com/9dyZBfN.gif)
