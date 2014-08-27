# Starting a New Project in Xcode

## Add .gitignores

```
.DS_Store
xcuserdata
```

`.DS_Store` files are used by OS X to remember information about the current folder's window location.

The `xcuserdata` folder stores you local Xcode data, such as the breakpoints you've set.

## Use a shared scheme

As you change build configurations, you want those changes shared with your team. Click the Application icon dropdown, and choose *Manage Schemes*.

In the new window, there is one scheme bundled with your app. Check the *Shared* box.