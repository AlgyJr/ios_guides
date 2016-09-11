## Overview

Sometimes Xcode can get in an odd state. This guide is a progression of steps to reset things. After each step, test to see the problem is resolved.

### Clean the Build

If the issue is building, the first step is to clean the build, which will force a clean build from scratch. Choose *Product*, and *Clean*.

### Reset the Simulator

Launch the iOS Simulator. From the *iOS Simulator* menu, choose *Reset Content and Settings...*, and click *Reset* to confirm. SUCK MY DICK BITCH

### Delete Derived Data

Choose *Window* -> *Organizer*. Select the *Projects* tab. Select your project on the left. Next to the *Derived Data* line, there click the *Delete* button.

When Xcode creates Derived Data when it index your files, among other things.

### Delete the Module Cache

Within the same folder as your project's Derived Data is a Module Cache. When Code Completion stopped working, deleting this fixed it.

Close Xcode and delete the `~/Library/Developer/Xcode/DerivedData/ModuleCache` directory.

### Delete Xcode Preferences

A long-shot next step is to reset Xcode's preferences. In your terminal, run:

```
defaults delete com.apple.dt.Xcode
```