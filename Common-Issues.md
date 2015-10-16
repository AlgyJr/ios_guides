While learning to develop in iOS, you will undoubtedly encounter many issues. Fortunately, many of these issues are caused by a handful of very common issues. Awareness of these common pitfalls along with knowledge of how to investigate clues given to us by Xcode, will help to get us out of trouble when we have errors or crashes.

### Compiler Errors vs. Run-Time Errors:

There are two main categories of errors, Compiler Errors and Run-Time Errors.

- **Compiler Errors**
   - **Symptoms:** You have little red stop signs in your file margins. As a result, Your app fails to build.
   - **Diagnose:** Click on the red stop signs to get hints on what and where the issue is.  

![compiler error gif](http://i.imgur.com/AsNRYvx.gif) 

- **Run-Time Errors**
   - **Symptoms:** Your app builds fine, but at a certain point it "crashes".
   - **Diagnose:** The first place to look is in the Debug area pane at the bottom of Xcode. The **Console**, (bottom right window), will likely have a long list of confusing "computer talk", fortunately, the useful information is found in the first paragraph all the way at the top, so get scrolling! The other place to look is in the **Variables View** to the left of the console.

![Run-Time Error gif](http://i.imgur.com/nciIGwV.gif) 

### Common Run-Time Issues

- **Error: unexpectedly found nil while unwrapping an Optional value:
- **Solve:** This basically means that sometime while your app was running, it was looking for something and instead got nothing, **nil**.
   - Step 1: We find our first clue is in the console. It tells us that something has no (nil) value. s
   - Step 2: We check the Variables Window next. When we expand the **self** heading, we see that, **pigImageView** = (UIImageView!) nil, "nil" being the keyword. This means ``pigImageView`` has no value, and Xcode is telling us that it should.
   - Step 3: We go to the swift file where that variable is declared. We notice that the little circle to the left where we make connections is empty. AH HA! Our code was trying to do something with the pigImageView, but it was not connected to the actual imageView in the Storyboard.
   - Solution: Reconnect by dragging from the empty circle to our pig imageView in Storyboard.  

![found nil gif](http://i.imgur.com/Z8oTiXy.gif)
