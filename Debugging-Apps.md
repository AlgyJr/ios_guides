# Debugging / Troubleshooting

## Overview

At the core of every engineering process is reiterating and fixing mistakes. For apps, there are a set of basic bugs:

1. Bugs that prevent the app from compiling and running
2. Bugs where the code compiles successfully, but the app crashes
3. Bugs where the code compiles and runs, but does not run as expected

There are many other possible bugs, and reasons for breaking down the code. Apple provides us with a set of tools to go about troubleshooting bugs and figuring out how to fix them.

The main set of debugging skills we'll go over are:

* [Fixing Warnings and Errors in the Issues Navigator](#fixing-warnings-and-errors)
* [Breakpoints and stepping through the code](#using-breakpoints)
* [Examining variables in the Variable View](#using-the-variable-view)
* [Using the console and print statements](#using-the-console-and-print-statements)
* [Using the native Debug View Hierarchy](#using-the-native-debug-view-hierarchy)

Apple also provides additional tools for testing code and other circumstances:

* [Using Playgrounds and Swift REPL](https://developer.apple.com/swift/blog/?id=18) (Read-eval-print-loop)
* [Simulating location](https://developer.apple.com/library/ios/recipes/xcode_help-debugger/articles/simulating_locations.html)
* [Instruments](https://developer.apple.com/library/watchos/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/index.html)

There are also third-party tools that have been created to aid in coding for iOS:

* Reveal
* Xcode plugins

Additionally, in a perfect world, the tools we are given would work flawlessly, but that is not the case. Xcode, itself, will some times be buggy. Please see [this section](#am-i-going-crazy-or-does-xcode-just-hate-me) for common gotchas and possible fixes.

Above everything, **always** test with an actual device. There is no substitute.


## Fixing Warnings and Errors

When you attempt to run your code, Xcode may report compiler warnings or errors. These messages will show up in a number of places.

On the toolbar at the top:

<a href="https://imgur.com/4uP4Bkg"><img src="https://i.imgur.com/4uP4Bkg.png" title="Toolbar warnings" /></a>

On the offending line of code in the editor:

<a href="https://imgur.com/CKGmdQp"><img src="https://i.imgur.com/CKGmdQp.png" title="Line of error" /></a>

And in the [Issues Navigator][issuesnavigator], on the left:

<a href="https://imgur.com/0aLHkFT"><img src="https://i.imgur.com/0aLHkFT.png" title="Issues Navigator errors" /></a>

As you're coding, Xcode will either give you warnings or error signals in the top toolbar and in your editor. They will also show up when your code fails to build. Clicking on the yellow warning or red error symbols in the top toolbar will bring up the Issues Navigator. Clicking on the items that show up in your Issues Navigator will bring up the offending line. It's generally a good rule of thumb to fix even the warnings, though they will not prevent you from compiling a successful build.


## Using Breakpoints

Even after writing an app, it doesn't always operate the way we expect it to. [Breakpoints][usingbreakpoints] are a tool used to better understand the flow and process of how an app works. We use breakpoints to pause the code at a specific line. With breakpoints, we can check what the exact state of the app is at a specific line.

To **add a breakpoint**, you click to the left of the line of code where you want to pause the app.

<a href="https://imgur.com/Bcbgwsf"><img src="https://i.imgur.com/Bcbgwsf.png" title="Adding a breakpoint" /></a>

You can track where you've added breakpoints across your entire project in the Breakpoints Navigator:

<a href="https://imgur.com/4gpNQ4X"><img src="https://i.imgur.com/4gpNQ4X.png" title="Breakpoints Navigator" /></a>

Once you've clicked to the left of the line, the section will be highlighted in blue. In order to disable it, click again. In order to delete it, right click, and select Delete Breakpoint. **Note that if the debug flag is gray instead of blue, breakpoints are deactivated.** In order to reactivate breakpoints, you can go to the Debug tab in the menu and select "Activate Breakpoints" or use the ⌘Y shortcut.

When you run your app with breakpoints activated, your app should pause when the line of code you've marked is called. In the screenshot above, the breakpoint is added in the viewDidLoad function. Thus when the view controller initially loads, the app will pause.

### Stepping through code

When you've activated breakpoints, and your app has paused, this bar becomes important:

<a href="https://imgur.com/QU2Azay"><img src="https://i.imgur.com/QU2Azay.png" title="Debugging bar" /></a>

The buttons allow you to control where you are in the app's flow. You can continue, step line by line, or step into and out of a function.

<a href="https://imgur.com/KGeEEmf"><img src="https://i.imgur.com/KGeEEmf.png" title="Stepping through code" /></a>

In the above code:

1. **Continue** would take you to `testing()` on line 20.
2. **Step Over** would take you to `print("first line of code")` on line 16
3. **Continue** and **Step Into** would take you inside `testing()` to `print("blahblah")` on line 24.

These actions can be accessed from either the Debugging bar or from the Debug menu of Xcode.


### Setting breakpoints for exceptions

Sometimes your app crashes, but it's not very informative as to where it crashes. Xcode may crash and point you to the autorelease pool in the main method or the AppDelegate file. Neither is very helpful in nailing down why your app just crashed.

However! We can set an [exception breakpoint][exceptionbreakpoint], so that Xcode will break at the exact line of code where the exception is thrown. Click the + on the bottom left of the Breakpoints Navigator, and select Add Exception Breakpoint...

<a href="https://imgur.com/GglwFsq"><img src="https://i.imgur.com/GglwFsq.png" title="Add Exception Breakpoint" /></a>

<a href="https://imgur.com/U82FRgW"><img src="https://i.imgur.com/U82FRgW.png" title="Configure Exception Breakpoint" /></a>

Now that you have an exception breakpoint set, Xcode will pause before throwing an exception error. Depending on the kind of error, this will be very informative or not.

Below is an example of where it would become informative. The variable array clearly does not contain any values, so this will throw an error.

<a href="https://imgur.com/SKCruas"><img src="https://i.imgur.com/SKCruas.png" title="Bad code example" /></a>

Without the exception breakpoint, Xcode will show you the AppDelegate file in the editor and simply print in the console:

`
	2016-02-12 15:16:27.908 Tip Calculator[27799:1365479] *** Terminating app due to uncaught exception 'NSRangeException', reason: '*** -[__NSArray0 objectAtIndex:]: index 2 beyond bounds for empty NSArray'
`

This is useful, and we can examine the stack trace to figure out where this array is. But with the exception breakpoint, Xcode will pause at line 18 exactly, to let us know something is wrong with this line of code. Now we know exactly where the NSRangeException occurs.


## The Debug Area (Console and Variable View)

Whenever your app crashes, the [Debug Area][debugarea] is where you will get information for what went wrong. This is the view that will automatically show whenever you use breakpoints or log a message with `print`.

We've gone over how to set breakpoints to essentially freeze the state of the app, but this doesn't give us much visibility into the code. The [Debug Area][debugarea] is how we poke around and get direct information about what the exact state of the app is.

<a href="https://developer.apple.com/library/mac/recipes/xcode_help-debugger/articles/about_debug_area.html"><img width="600" src="https://developer.apple.com/library/mac/recipes/xcode_help-debugger/Art/debug_area-c_debugging_2x.png" title="Debug area explanation" /></a>

### Using the Variable View

When the app hits a breakpoint, the [Variable View][variableview] shows you all the variables immediately available in the context where the app is paused. 

This list lets you know what you can use to console to print out.

From the Variable View, you can do a number of things:

* Print variable descriptions
* Cast variables into a different data type to view (Int to hex or byte)
* Directly change a value during runtime
* Add "watchpoints" to watch how a variable's value changes over the lifetime of the app
* View the memory of variables

Please see the [apple documentation][variableview] for further advanced functionality.

### Using the console and print statements

If we use `print("testing")` in our code, "testing" will be printed out in the [console][usingconsole] area. When an app crashes, the console immediately prints out the error message, exception, and stack trace to help us troubleshoot and understand the crash. When debugging, we use the [console][usingconsole] to check for error messages and other logging.

The [console][usingconsole] is mainly used to interact with [lldb][lldbxcode], the debugger that Xcode utilizes. We can use the console to control the debugger and step through the code, instead of using the bar at the top of the Debug Area.

The two common commands for displaying variable data are `p` and `po`, or print and print-object.

The print command (**p**) allows you to print information about an address, a variable, or a method.

The print-object command (**po**) takes an Objective-C object as its argument, and calls the object's description method.

## Using the native Debug View Hierarchy

As we are dealing with UI in an app, not just inputs and outputs, we also find the [Debug View Hierarchy][debugviewhierarchy] to be a useful tool. As you're running your app in Xcode, it's possible to tap on the following button to see how your app is laid out.

<a href="https://developer.apple.com/library/tvos/documentation/DeveloperTools/Conceptual/debugging_with_xcode/chapters/special_debugging_workflows.html#//apple_ref/doc/uid/TP40015022-CH9-SW2"><img width="300" src="https://developer.apple.com/library/tvos/documentation/DeveloperTools/Conceptual/debugging_with_xcode/Art/dbgah-debug_button_bar-vdbgr_2x.png" title="Debug area explanation" /></a>

The [Debug View Hierarchy][debugviewhierarchy] provides you with a 3D view of the app. You can visually troubleshoot whether the view you're unable to see is:

1. Not initialized
2. Has a frame of 0 height and 0 width
3. Covered by another subview

It's an extremely useful tool.

## Am I going crazy or does Xcode just hate me?

It's possible that Xcode did not get the memo, and your code does not work properly. Most commonly, this appears to be the case with the Interface Builder. Sometimes this happens with the Simulator.

Common Gotchas:

* I fixed an error, but errors and warnings aren't disappearing.
	* Xcode seems to be a bit buggy. Try building the app, as that is the final word on whether there's an error present or not.
* I added a UIButton, but I can't tap it.
	* Make sure it's *actually* there. For some reason, sometimes the xib/nib/storyboard will not properly instantiate a view. Moving it around may resolve this.
* I'm using MapKit and memory usage is skyrocketing
	* If you using the simulator, try it on an actual device instead. You may find that you don't actually have a memory leak.
 

If nothing seems to work, and you're flat out of ideas, try [resetting Xcode](https://github.com/codepath/ios_guides/wiki/Fixing-Xcode).

[issuesnavigator]: https://developer.apple.com/library/ios/recipes/xcode_help-issue_navigator/articles/viewing_issues_in_the_issue_navigator.html

[usingbreakpoints]: https://developer.apple.com/library/ios/recipes/xcode_help-source_editor/chapters/Creating,Disabling,andDeletingBreakpoints.html

[exceptionbreakpoint]: https://developer.apple.com/library/ios/recipes/xcode_help-breakpoint_navigator/articles/adding_an_exception_breakpoint.html

[debugarea]: https://developer.apple.com/library/mac/recipes/xcode_help-debugger/articles/about_debug_area.html

[usingconsole]: https://developer.apple.com/library/tvos/documentation/DeveloperTools/Conceptual/debugging_with_xcode/chapters/debugging_tools.html#//apple_ref/doc/uid/TP40015022-CH8-SW10

[variableview]: https://developer.apple.com/library/tvos/documentation/DeveloperTools/Conceptual/debugging_with_xcode/chapters/debugging_tools.html#//apple_ref/doc/uid/TP40015022-CH8-SW9

[debugviewhierarchy]: https://developer.apple.com/library/tvos/documentation/DeveloperTools/Conceptual/debugging_with_xcode/chapters/special_debugging_workflows.html#//apple_ref/doc/uid/TP40015022-CH9-SW2

[lldbxcode]: https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/Introduction.html
