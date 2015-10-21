It is often convenient to hold views within other views. When a (child) view is inside of another (parent) view, it is considered to be "nested" within that view. Among many other things, this allows us to easily move or animate all the nested views by simply moving or animating the parent view.

### Step 1: Add the Parent View.
- Drag a UIView from the Object Library onto your ViewController in the Storyboard.
- If you don't want the view to be seen, you can set it's background default.  
  
![Add Parent View](http://i.imgur.com/EiFqzJw.gif)  

### Step 2: Nest the Child Views. 
- Method 1: Drag the views you want to be nested from the Object Library into the parent view in the Storyboard. 
   
![Nest views storyboard](http://i.imgur.com/xO3D7Zs.gif)
- Method 2: Drag the views you want to be nested from the Object Library into the Document Outline.
  
![Nest views Document Outline](http://i.imgur.com/KYwswIt.gif)  
  
- You can change which views are in front or back (View Hierarchy) by adjusting there order in the Document Outline.  
  
![Change view hierarchy in Document Outline]() 
