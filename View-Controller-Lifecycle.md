
**init(coder:)**

- When it is called / When it is used : 
    - View controller are usually created from storyboards.When this is the case,init(coder:) is the initializer that gets called and you have to override.
    - If provides an NSCoder instance as a parameter, which you need only if you are using iOS serialization APIs. This is not used often, so you can ignore the parameter. If you are curious, serialization transforms an object in a byte stream that you can save on disk or send over the network.

- Application : 
    - During the initalization phase of a view controller, you usually allocate the resources that the view controller will need during its lifetime. These include model objects or other auxiliary controllers, like network controllers.
    - Previous view controllers might also pass these objects to the current one, so you do not always need to instantiate them in every view controller.

- Things to take care of :  
    - Beware that the view of the view controller has still not been instantiated at this point. If you try to access it through the view property of UIViewController, theloadView() method will be called. This might create unexpected behavior and bugs, so it’s safer not to access the view until later in the lifecycle.

**init(nibName:bundle:)**

- When it is called / When it is used :
Sometimes you might decide to put your view controller’s interface in a separate nib file instead of a storyboard. This might happen, for example, to work in a larger team where different members need to be change view controller interfaces without affecting the work of others. You also might have a project that was created when storyboards did not exist yet, so every view controller had its own nib file.Keep in mind that if your main storyboard starts getting too big, you can split it into more storyboards. You do not need to move every view controller in a separate nib file.

- If you create a view controller from a nib file, this initializer is called instead of init(coder:).

**loadView()**

- This is the method that creates the view for the view controller. You override this method only in case you want to build the whole interface for the view controller from code. Don’t use this unless there is a valid reason.
- If you are working with storyboards or nib files you do not have to anything with this method and you can ignore it. Its implementation in UIVIewController loads the interface from the interface file and connects all the outlets and actions for you.

**viewDidLoad()**

- When this method gets called, the view of the view controller has been created and you are sure that all the outlets are in place.
- Application : 
    - It is common to use this method to populate the user interface of the view controller with data before the user sees it.
    - It is also a good place where to start some background activity where you need to have the user interface in place at the end.
    - A common case are network calls that you need to do only once when the screen is loaded.
    - Good place to init and setup objects used in the viewController.

- ViewDidLoad V/S ViewDidAppear : 
    - If you need to repeat them (background activity/ UI changes/ make network calls ) to update the data in the view controller ,viewDidAppear(_:) is more appropriate to do so.

- Important thing to remember : 
    - This method is called only once in the lifetime of a view controller, so you use it for things that need to happen only once. If you need to perform some task every time a view controller comes on screen, then you need the next method.
    - Keep in mind that in this lifecycle step the view bounds are not final.

**viewWillAppear(_:)**

- You override this method for tasks that need you need to repeat every time a view controller comes on screen. This method can be called multiple times for the same instance of a view controller.
- Notifies the view controller that its view is about to be added to a view hierarchy
- Application : 
    - Usually you use this method to update the user interface with data that might have changed while the view controller was not on the screen.
    - You can also prepare the interface for animations you want to trigger when the view controller appears.
- ViewDidLoad V/S ViewDidAppear : 
    - Code you need to execute only once should go into an initializer or viewDidLoad().
    - In this step the view has bounds defined but the orientation is not applied



**viewWillLayoutSubViews:**
- Called to notify the view controller that its view is about to layout its subviews.
- This method is called every time the frame changes like for example when rotate or it’s marked as needing layout. It’s the first step where the view bounds are final.
- If you are not using autoresizing masks or constraints and the view size changes you probably want to update the subviews here.

**viewDidLayoutSubviews:** 

- Called to notify the view controller that its view has just laid out its subviews.
- Make additional changes here after the view lays out its subviews.

**viewDidAppear(_:)**

- This method gets called after the view controller appears on screen.
- You can use it to start animations in the user interface, to start playing a video or a sound, or to start collecting data from the network.

**viewWillDisappear(_:)**

- Before the transition to the next view controller happens and the origin view controller gets removed from screen, this method gets called.
- You rarely need to override this method since there are few common tasks that need to be performed at this point, but you might need it.

**viewDidDisappear(_:)**

- After a view controller gets removed from the screen, this method gets called.
- You usually override this method to stop tasks that are should not run while a view controller is not on screen.
- For example, you can stop listening to notifications, observing other objects properties, monitoring the device sensors or a network call that is not needed anymore.

**deinit()**

- Like every other object, before a view controller is removed from memory, it gets deinitialized.
- Application :

    - You usually override deinit() to clean resources that the view controller has allocated that are not freed by ARC.
    -  You can also stop tasks you did not stop in the previous method because you wanted to keep them in the background.
- Beware :

    - A view controller going out of the screen does not mean that it will be deallocated afterwards. Many containers keep view controllers in memory. For example, as you go deeper in a navigation controller, all the previous view controllers stay in memory. A navigation controller releases view controllers only when navigating back up the hierarchy. For this reason, you have to keep in mind that a view controller that is not on the screen still works normally and receives notifications. Sometimes this is desiderable, other times it isn’t, so you need to keep this in mind while developing your app.

**didReceiveMemoryWarning()**

- iOS devices have a limited amount of memory and power. When the memory starts to fill up, iOS does not use its limited hard disk space to move data out of the memory like a computer does. For this reason you are responsible to keep the memory footprint of your app low. If your app starts using too much memory, iOS will notify it.

- Since view controllers perform resource management, these notifications are delivered to them through this method. In this way you can take actions to free some memory. Keep in mind that if you ignore memory warnings and the memory used by your app goes over a certain threshold, iOS will end your app. This will look like a crash to the user and should be avoided.