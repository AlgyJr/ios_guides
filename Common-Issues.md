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

- **Error
