---
date: 2023-04-29
title: How to Use TalkBack on an Android Emulator
categories:
  - Android
  - Accessibility
  - Testing
author_staff_member: aj
featured_image: talkback/android-accessibility-suite.png
image:
  path: images/talkback/android-accessibility-suite.png
---

A huge part of making our apps accessible is enabling access for those who have limited vision. This can range from allowing users to adjust the size of the UI or the text in our apps - to making sure that accessibility tools like screen readers can parse the UI of our apps.

![Android Accessibility Suite Logo on background gradient](/images/talkback/android-accessibility-suite.png)

The screen reading tool provided by Google on Android devices is called TalkBack. This app is usually installed by default on Android devices, letting users operate their phones without needing to see the screen. For developers, we can use TalkBack to [ensure our app is accessible](https://developer.android.com/guide/topics/ui/accessibility).

However, you may have noticed that this behavior isn't available by default when you create a new Android Emulator in Android Studio - a problem for developers & testers that don't have access to physical devices.

Let's walk through the process of getting TalkBack set up on your Android Emulators.

## Create A New Emulator

In Android Studio (I'm using Android Studio Flamingo 2022.2.1), open the Device Manager and choose Create Device.

### Select Hardware

Select a device you'd like to create an Emulator of, **making sure to select one that has Play Store access**. You can tell by the Play Store icon visible in the 'Play Store' column of the selection list.

![selecting play store enabled device screen shot](/images/talkback/select-play-store-device.png)

After selecting the appropriate hardware, click Next.

### System Image

Select or download an Android OS image. I chose to download & select Android Tiramisu (API 33), though any modern OS will do.

![selecting system image screen shot](/images/talkback/system-image.png)

Once that is downloaded & selected, click Next.

### AVD Setup

We've done all the hard work, no changes needed here unless you need to make specific adjustments to your Emulator setup. Click Finish.

## Download the Android Accessibility Suite

Back in Device Manager, click the 'play' button on your newly added device to run it.

If, at this point, we decided to go into Settings and look for TalkBack, we won't get any results. This is the default behavior for all new emulators you create. Let's fix that by downloading the [Android Accessibility Suite](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback&hl=en_US&gl=US).

### Sign In & Search

Your device should have the Play Store app already installed. Open it, and sign in with your Google account. You may have to go through a few setup steps when logging in to the Play Store, as this emulator is treated as a 'new' device.

In the Play Store search for 'android accessibility suite' and download it.

![searching for android accessibility suite](/images/talkback/aas-search.png)

### Using TalkBack

After it downloads and installs, close the Play Store and look at your list of installed apps. You may notice that the Accessibility Suite doesn't show up here. That's expected for this very specific app.

Instead, open Settings, and search for TalkBack. Here you'll see the TalkBack option, which brings you to the Screen Reader section of the device's Accessibility settings.

![accessibility settings on device with TalkBack](/images/talkback/talkback-settings.png)

Ta-da! 🥳 Now we can use TalkBack just like we would on a normal Android device.

![talkback landing page](/images/talkback/talkback-page.png)

### TalkBack Resources

For more on testing your apps for accessibility with TalkBack, check out these great resources:

- [TalkBack - Accessibility on Android (video)](https://www.youtube.com/watch?v=_1yRVwhEv5I)
- [Testing with TalkBack](https://accessibility.huit.harvard.edu/test-android-talkback)
- [Google's Guide to Testing Accessibility](https://developer.android.com/guide/topics/ui/accessibility/testing)

Go forth and build accessible apps!