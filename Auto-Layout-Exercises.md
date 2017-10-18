These are a few basic and more complicated Auto Layout exercises that demonstrate view hierarchies with an average layout complexity.

### Exercise 1 - Basic Auto Layout

![Basic|200](https://i.imgur.com/rTmdGd4.jpg)&nbsp;&nbsp;![Basic|200](https://i.imgur.com/puyV4F2.jpg)

- [Download the starter project](https://www.dropbox.com/s/b3d93tqpokuxg7s/BasicAutoLayout%20-%20Incomplete.zip?dl=0).
- FirstViewController is created using Interface Builder. Add Auto Layout constraints so that the following conditions are met when switching between an iPhone simulator and an iPad simulator:
   - The gray view has fixed left and right margins (i.e., grows in width), but has a fixed height.
   - The blue view is pinned below the gray view and to the right side of the superview. It has a fixed height and width.
   - The label is pinned to the left side of the superview and the left side of the blue view.
   - The red view is pinned to the bottom of the superview and is horizontally centered.
- SecondViewController is created using programmatic view code. Add the same constraints as above using the visual formatting language.

### Exercise 2 - Yelp

![Yelp Before|200](https://i.imgur.com/BD38xL6.jpg)&nbsp;&nbsp;![Yelp After|200](https://i.imgur.com/Jp3DP4T.jpg)

1. [Download the starter project](https://www.dropbox.com/s/iqlj4wtt9o23brs/Yelp%20-%20Incomplete.zip?dl=0) which should look like the image on the left in the simulator.
2. Add the appropriate Auto Layout constraints in the custom cell. Here are some hints:
   - Start from the top of the cell and clear the warnings as you move down the cell.
   - You'll need to configure the Content Hugging Priority to resolve the Auto Layout warning between the title label and the distance label.
   - You'll need to configure the Compression Resistence Priority before the title will start wrapping.
   - You'll need to set maxPreferredContentWidth in any multi-line label to the width of the label in the layoutSubviews and awakeFromNib methods.
   - Remember to configure the rowHeight of the UITableView to be UITableViewAutomaticDimension
