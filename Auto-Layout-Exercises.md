These are a few basic and more complicated Auto Layout exercises that demonstrate view hierarchies with an average layout complexity.

### Exercise 1 - Basic Auto Layout

- Open ...
- In Interface Builder
   - Configure the view to stretch in width
   - Center
   - Pin to right
   - Pin to Bottom
- In code
   - Configure the view to stretch in width
   - Center
   - Pin to right
   - Pin to Bottom

### Exercise 2 - Yelp

![Yelp Before|250](http://i.imgur.com/BD38xL6.jpg)&nbsp;&nbsp;![Yelp After|250](http://i.imgur.com/Jp3DP4T.jpg)

1. [Download the starter project](https://www.dropbox.com/s/iqlj4wtt9o23brs/Yelp%20-%20Incomplete.zip?dl=0) which should look like the image on the left in the simulator.
2. Add the appropriate Auto Layout constraints in the custom cell. Here are some hints:
   - Start from the top of the cell and clear the warnings as you move down the cell.
   - You'll need to configure the Content Hugging Priority to resolve the Auto Layout warning between the title label and the distance label.
   - You'll need to configure the Compression Resistence Priority before the title will start wrapping.
   - You'll need to set maxPreferredContentWidth in any multi-line label to the width of the label in the layoutSubviews and awakeFromNib methods.
   - Remember to configure the rowHeight of the UITableView to be UITableViewAutomaticDimension
