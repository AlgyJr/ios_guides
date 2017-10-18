## Carousel

The purpose of this homework is to leverage views, view properties, and events to create a high fidelity prototype that is difficult to distinguish from a production app. We're going to use the techniques from this week to implement the Carousel app from the signed out state to the basic signed in state.

Time spent: `<Number of hours spent>`

### Features

#### Required

- [ ] Static photo tiles on the initial screen.
- [ ] Sign In.
  - [ ] Tapping on email/password reveals the keyboard and shifts the scrollview and Sign In button up.
  - [ ] User sees an error alert when no email is present or no password is present.
  - [ ] User sees a loading screen upon tapping the Sign In button.
  - [ ] ```alertView.dismissWithClickedButtonIndex(0, animated: true)``` dismisses the loading screen with no buttons.
  - [ ] User sees an error alert when entering the wrong email/password combination.
  - [ ] User is taken to the tutorial screens upon entering the correct email/password combination.
- [ ] Tutorial Screens.
  - [ ] User can page between the screens.
- [ ] Image Timeline.
  - [ ] Display a scrollable view of images.
  - [ ] User can tap on the conversations button to see the conversations screen (push).
  - [ ] User can tap on the profile image to see the settings view (modal from below).
- [ ] Settings.
  - [ ] User can dismiss the settings screen.
  - [ ] User can log out.



#### Optional

- [ ] Photo tiles move with scrolling.
- [ ] Sign In
  - [ ] When the keyboard is visible, if the user pulls down on the scrollview, it will dismiss the keyboard
  - [ ] On appear, scale the form up and fade it in.
- [ ] Sign Up
  - [ ] Tapping in the form reveals the keyboard and shifts the scrollview and "Create a Dropbox" button up.
  - [ ] Tapping the Agree to Terms checkbox selects the checkbox.
  - [ ] Tapping on Terms shows a webview with the terms.
  - [ ] User is taken to the tutorial screens upon tapping the "Create a Dropbox" button.
- [ ] Tutorial Screens.
  - [ ] User can page between the screens with updated dots.
  - [ ] Upon reaching the 4th page, hide the dots and show the "Take Carousel for a Spin" button.
- [ ] Learn more about Carousel.
  - [ ] Show the "Learn more about Carousel" button in the photo timeline.
  - [ ] Tap the X to dismiss the banner.
  - [ ] Track the 3 events:
    - [ ] View a photo full screen.
    - [ ] Swipe left and right.
    - [ ] Share a photo.
  - [ ] Upon completion of the events, mark them green.
  - [ ] When all events are completed, dismiss the banner.

### Walkthrough

![Video Walkthrough](https://i.imgur.com/TBJkE46.gif)

Credits
---------
* Any libraries or borrowed content.
