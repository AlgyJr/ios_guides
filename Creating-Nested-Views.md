It is often convenient to hold views within other views. We can nest views by putting a view inside of another view. In this case, the inner view is the **subview** and the outer view is the **superview**. The relationship of subview and superview is relative and in many cases, a view that is a superview to a view it contains, is also a subview to view that it is itself contained by. It can be helpful, when animating multiple views, to group them together inside a containing superview so we don't have to animate each view individually; instead, we can just animate the superview, and the subviews will go along for the ride!

### Step 1: Add the Superview.
- Drag a UIView from the Object Library onto your ViewController in the Storyboard.
- If you don't want the view to be seen, you can set it's background to clear color.  
  
![Add Parent View](http://i.imgur.com/EiFqzJw.gif)  

### Step 2: Add the Subviews. 
- Method 1: Drag the views you want to be add inside your superview from the Object Library into the parent view in the Storyboard. 
   
![Nest views storyboard](http://i.imgur.com/xO3D7Zs.gif)

- Method 2: Drag the views you want to be nested from the Object Library into the Document Outline.
  
![Nest views Document Outline](http://i.imgur.com/KYwswIt.gif)  

- Note: If you have image assets that you want to use, go to the Media Library and drag the image you want from there. The image will automatically be put into an image view as you drag it onto the storyboard or document outline.

### Step 3: Change View Hierarchy  
- You can change which views are in front or back (View Hierarchy) by adjusting there order in the Document Outline.
- Views below other views in the Document Outline appear in front.    
  
![Change view hierarchy in Document Outline](http://i.imgur.com/EMz8KjY.gif) 
