### Overview

There are three basic types of Navigation mechanism in iOS :

1. Push Navigation
2. Modal Navigation
3. Tab Navigation

### Push Navigation

A push Segue is adding another VC to the navigation stack. This assumes that VC that originates the push is part of the same navigation controller that the VC that is being added to the stack belongs to. Memory management is not an issue with navigation controllers and a deep stack. As long as you are taking care of objects you might be passing from one VC to another, the compiler will take care of the navigation stack. See the image for a visual indication:

![Push Navigation](https://i.imgur.com/JBrxKdh.gif)

### Modal Navigation

A modal Segue is just one VC presenting another VC modally. The VCs don't have to be part of a navigation controller and the VC being presented modally is generally considered to be a "child" of the presenting (parent) VC. The modally presented VC is usually sans any navigation bars or tab bars. The presenting VC is also responsible for dismissing the modal VC it created and presented.

![Modal](https://i.imgur.com/KpX7FcB.gif)

### Tab Bar Navigation

Tab bar controllers are simple to use navigation controllers. They are generally used, when there are different logical flow in your app and you want to switch from one controller to another.  

![Tab bar](https://i.imgur.com/WhoidE5.gif)
