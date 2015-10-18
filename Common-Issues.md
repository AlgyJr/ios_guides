While learning to develop in iOS, you will undoubtedly encounter many issues. Fortunately, many of these issues are caused by a handful of very common issues. Awareness of these common pitfalls along with knowledge of how to investigate clues given to us by Xcode, will help to get us out of trouble when we have errors or crashes.

## Compiler Errors vs. Run-Time Errors:

There are two main categories of errors, Compiler Errors and Run-Time Errors.

#### Compiler Errors
   - **Symptoms:** You have little red stop signs in your file margins. As a result, Your app fails to build.
   - **Diagnose:** Click on the red stop signs to get hints on what and where the issue is.  

![compiler error gif](http://i.imgur.com/AsNRYvx.gif) 

#### Run-Time Errors
   - **Symptoms:** Your app builds fine, but at a certain point it "crashes".
   - **Diagnose:** Go to the Debug area pane at the bottom of Xcode.
      - 1:  The **Console**, (bottom right window), will likely have a long list of confusing "computer talk", fortunately, the useful information is found in the first paragraph all the way at the top, so get scrolling! 
      - 2: The second place to look is in the **Variables View** to the left of the console. 

![Run-Time Error gif](http://i.imgur.com/nciIGwV.gif) 

## Common Run-Time Crashes

#### 1. Error: unexpectedly found nil while unwrapping an Optional value
- **Translation:** This basically means that sometime while your app was running, it was looking for something and instead got nothing, **nil**.
- **Common Cause** An outlet was created but got disconnected.
- **Diagnose:**   
   - **Step 1:** We find our first clue is in the console. It tells us that something has no (nil) value. s
   - **Step 2:** We check the Variables Window next. When we expand the **self** heading, we see that, **pigImageView** = (UIImageView!) nil, "nil" being the keyword. This means ``pigImageView`` has no value, and Xcode is telling us that it should.
   - **Step 3:** We go to the swift file where that variable is declared. We notice that the little circle to the left where we make connections is empty. AH HA! Our code was trying to do something with the pigImageView, but it was not connected to the actual imageView in the Storyboard.
- **Solution:** Reconnect by dragging from the empty circle to our pig imageView in Storyboard.  

![found nil gif](http://i.imgur.com/Z8oTiXy.gif)

#### 2. Error: this class is not key value coding-compliant for the key...
- **Translation:** Basically, there is a connection somewhere behind the scene that is looking for a specific **outlet** but is not finding it.
- **Common Cause:** The name of an outlet was changed or deleted but the connection is still there.
- **Diagnose:**
   - **Step 1:** Scroll all the way to the top of the Console to find the useful information.
      - The ViewController where the error is occurring.
      - The Key **Value Coding-compliant** tells you there is a **connections issue**.
      - the name after **for the key** is what you will be looking for when you investigate connections.
   - **Step 2:** Select the offending ViewController by clicking on the *yellow dot*. Open the **Utilities** pane and select the **Connections Inspector**.
- **Solution:** Delete the bad connection by clicking the little "x" between the connections. The bad connection should have an "!" on the right side instead of a little circle.

![key value coding gif](http://i.imgur.com/aLwKVKa.gif)

#### 3. Error: Unknown class (Some ViewController) in Interface Builder File.
- **Translation** A custom ViewController Swift file you made is not configured right.
- **Common Cause** You assigned your ViewController to a Custom Class (In Utilities -> Identity Inspector) before actually creating the Custom Swift ViewController File.
- **Diagnose:**
   - **Step 1:** Check the console to see which ViewController file is having the issue.
- **Solution:** Click on the offending ViewController's yellow dot in Storyboard. Navigate to the Identity Inspector in the Utilities Pane. In the "Custom Class" section, click in the **Module** field. Press Enter, the Module field should change to "Current (Your App Name)" in a light grey text. Run your app.  

![Unknown Class](http://i.imgur.com/in2kY8A.gif)
## Unintended Run-Time Behaviors

#### 1. Scroll View has weird gap.
- **Solution:** Click on View Controller that contains the Scroll View, navigate to the Attributes Inspector (DJ Slider) inside the Utilities Pane. **Uncheck, "Adjust Scroll View Insets"**.  

![Adjust Scroll View Insets gif](http://i.imgur.com/lQH6X5r.gif)

#### 2. Image Views don't respond to your gestures.
- **Solution:** Click on the Image View where the Gesture Recognizer has been adde and navigate to the Attributes Inspector (DJ Slider) inside the Utilities Pane. **Check, "User Interaction Enabled"**.  

![User Interaction Enabled](http://i.imgur.com/GciPd8b.gif)  

#### 3. App stops due to accidental breakpoint.  
Breakpoints are an essential debugging tool, however it's easy to accidentally set a breakpoint and cause what appears to be a crash at run-time.  
-**Solution:** Navigate to the "gutter" of the ViewController file that has the accidental break point (Far left side of the file). *Right Click* or *ctrl + click* on the blue breakpoint tag and select, "Delete Breakpoint" from the dropdown menu. 


## Common Compiler Errors

#### 1. Use of unresolved identifier...
- **Translation** There is a word that Xcode does not recognize entered as a variable or constant.
- **Common Causes** 
   - 1. You forgot to define the variable first using **var** or as a constant using **let**.
   - 2. You did not define the variable or constant up top as a "global" variable and are trying to reference the variable in different places throughout your Swift file.
   - 3. You misspelled the variable or constant.  

![unresolved identifier gif](http://i.imgur.com/LtK2MgR.gif)