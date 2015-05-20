## Mailbox

The purpose of this homework is to leverage animations and gestures to implement more sophisticated interactions. We're going to use the techniques from this week to implement the Mailbox interactions.

Time spent: `<Number of hours spent>`

### Features

#### Required

- [ ] On dragging the message left:
  - [ ] Initially, the revealed background color should be gray.
  - [ ] As the reschedule icon is revealed, it should start semi-transparent and become fully opaque. If released at this point, the message should return to its initial position.
  - [ ] After 60 pts, the later icon should start moving with the translation and the background should change to yellow.
    - [ ] Upon release, the message should continue to reveal the yellow background. When the animation it complete, it should show the reschedule options.
  - [ ] After 260 pts, the icon should change to the list icon and the background color should change to brown.
    - [ ] Upon release, the message should continue to reveal the brown background. When the animation it complete, it should show the list options.

- [ ] User can tap to dismissing the reschedule or list options. After the reschedule or list options are dismissed, you should see the message finish the hide animation.
- [ ] On dragging the message right:
  - [ ] Initially, the revealed background color should be gray.


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

![Video Walkthrough](http://i.imgur.com/LQwqd7z.gif)

Credits
---------
* Any libraries or borrowed content.