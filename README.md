# iOS Pre-work: Tip Calculator App

This pre-work incorporates the following steps:

1. Setup Xcode
2. Familiarize yourself with Swift
3. Build the initial Tip Calculator 
4. Add a Settings Screen to the Tip Calculator
5. Submit your app for review via GitHub
6. Extend your app, improve UI, add features

## 1. Setup Xcode
[Download](https://itunes.apple.com/us/app/xcode/id497799835?mt=12#) Xcode from the Mac App Store.

**Note:** Membership in the iOS Developer Program is **not** required. This program costs $99 a year and is only required if you want to distribute your apps in the App Store and have access to beta iOS releases.	

## 2. Familiarize yourself with Swift
There are a bunch of resources out there to learn Swift. We recommend taking a look at the following options:

* Apple has the an [official Swift guide](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/index.html).
* Check out the [1 hour Introduction to Swift video](https://developer.apple.com/videos/wwdc/2014/?id=402). 
* You can also find more resources [here](https://developer.apple.com/swift/resources/).

## 3. Build the initial Tip Calculator 
Follow this [video walkthrough](https://vimeo.com/102084767).
   - **Note that there is one thing that changed in the beta release that doesn't work anymore:** At the **21:10** minute mark of the video, replace: 
     - `var billAmount = billField.text.bridgeToObjectiveC().doubleValue`
   - **With:**
     - `var billAmount = NSString(string: billField.text!).doubleValue`

## 4. Add a Settings Screen to the Tip Calculator

### Add a Navigation Bar to the main view

On the Interface Builder, select `TipViewController`, then embed it in a navigation controller (Editor -> Embed In -> Navigation Controller)
  <img src='http://i.imgur.com/vib8afk.gif' title='Add a navigation bar' width='' alt='Add a navigation bar' />

### Create a Settings View Controller
Every screen in your application is implemented by a view controller. A view controller has two parts: the designed part that's in the Storyboard and an Swift class that contains the code for that screen.

If a screen in the application doesn't require any code, then it can use the default UIViewController. However, as soon as you need to handle events like a user tapping on the screen, then you need to create a custom view controller for that screen.

1. For our settings screen, we need a custom view controller (`SettingsViewController`):
    - Create the class (File -> New -> File -> Cocoa Touch Class) with the following options:
        <img src='http://i.imgur.com/aSZn5rX.png' title='Imgur' width='' alt='Imgur' />
    - Then, use the "Object Library" to add a View Controller to the `Main.storyboard` and associate it with the `SettingsViewController` class:

		<img src='http://i.imgur.com/hDivZqQ.gif' title='Imgur' width='' alt='Imgur' />
2. Now we need a way to get from our main screen to the settings screen:
	- We'll do this by adding a button to the navigation bar:
  	    
  	    <img src='http://i.imgur.com/oayP1dh.png' title='Imgur' width='' alt='Imgur' />
	- Let's go back to the `Main.storyboard` and use the "Object Library" to add a `BarButtonItem` to the navigation bar. Tapping on this button should "segue" us to the `SettingsViewController`:

        <img src='http://i.imgur.com/UsKyUhX.gif' title='Imgur' width='' alt='Imgur' />
    - **Note:** You'll need to ctrl-drag from the `BarButtonItem` to the `SettingsViewController` to create the push segue.

**Additional info:** In iOS, navigation stacks are one of the most common navigation mechanisms. You've seen it in many apps when you tap a button a new view slides in from the right. When you tap the back button, the previous view slides in from the left. A UINavigationController maintains the stack of view controllers and you can push new view controllers onto the stack. The back button is automatically added and tapping on the back button will pop the most recent view controller.

### Designing the Settings Screen

The purpose of the settings screen is to configure the default tip percentage. In the `SettingsViewController` we added to the Main Storyboard, design what you think the SettingsViewController should look like.

### Loading and Saving

There are several different persistence mechanisms in iOS. The simplest to use is a persistent key-value store called [NSUserDefaults](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class). You might use NSUserDefaults for similar purposes as cookies in web development. They can store things like application settings, the current user, or a flag for whether a user has already seen a helpful hints popover.

To save a key to NSUserDefaults, do something like this:

```swift
let defaults = NSUserDefaults.standardUserDefaults()
defaults.setObject("some_string_to_save", forKey: "some_key_that_you_choose")
defaults.setInteger(123, forKey: "another_key_that_you_choose")
defaults.synchronize()
```

To load a key from NSUserDefaults, do something like this:

```swift
let defaults = NSUserDefaults.standardUserDefaults()
let stringValue = defaults.objectForKey("some_key_that_you_choose") as! String
let intValue = defaults.integerForKey("another_key_that_you_choose")
```

Note the `synchronize` call. NSUserDefaults automatically and periodically synchronizes, but to manually flush the keys and values to disk, call [synchronize](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/occ/instm/NSUserDefaults/synchronize) to guarantee that your updates are saved.

### View Controller Lifecycle

When returning to the main `TipViewController` from the settings screen, it would be good to have the tip percentage reflect the new default value. One way to do that is load the tip percentage from NSUserDefaults whenever the view appears.

A view controller has a series of "lifecycle" methods that are called at various stages. When the view controller is initially set up, `viewDidLoad` is called. As it is shown `viewWillAppear` and `viewDidAppear` are called. As it is hidden `viewWillDisappear` and `viewDidDisappear` are called. The lifecycle methods are split into two methods (i.e. `viewWillAppear` and `viewDidAppear`) because sometimes you want some behavior to happen before the transition animation starts or after the transition animation ends.

In `TipViewController.swift`, try adding the following methods and watch the console output as you navigate into the settings view and back.
```swift
override func viewWillAppear(animated: Bool) {
    super.viewWillAppear(animated)
    print("view will appear")
}

override func viewDidAppear(animated: Bool) {
    super.viewDidAppear(animated)
    print("view did appear")
}

override func viewWillDisappear(animated: Bool) {
    super.viewWillDisappear(animated)
    print("view will disappear")
}

override func viewDidDisappear(animated: Bool) {
    super.viewDidDisappear(animated)
    print("view did disappear")
}
```

### Animating View Properties (Optional)

Many UIView properties can be animated, including frame, center, backgroundColor, alpha, transform, etc.

In order to animate one or more views, simply create an animation block. The animation block will animate from the current value to the value that's set within the block. You can manipulate multiple views within the block. For example, in the snippet below, firstView is fading in, while secondView is fading out. This is assuming that you've created outlets for firstView and secondView.

```swift
// Optionally initialize the property to a desired starting value
self.firstView.alpha = 0
self.secondView.alpha = 1
UIView.animateWithDuration(0.4, animations: {
    // This causes first view to fade in and second view to fade out
    self.firstView.alpha = 1
    self.secondView.alpha = 0
})     
```

## 5. Submitting the Project

Once you've completed the Tip Calculator application (**including adding a settings screen**), please [push your app via Github](https://help.github.com/articles/create-a-repo). All submissions occur via Github and we require each submission to follow a particular format which includes a **clearly documented README** with a GIF walkthrough and **an issue raised** as detailed below.

<img src='https://github.com/Purdue-iOS-Dev-Club/TipCalculator/blob/master/submit.png' title='Submitted Issue' width='' alt='Submitted Issue' />

Submit your project by **creating an issue on your repository** and adding the Github handle (**@hao44le**) to the issue body so we can review the submission. Also be sure to **include a README** containing a GIF walkthrough using [LiceCap](http://www.cockos.com/licecap/) of the app demonstrating how it works with required user stories completed. Use [this README template](../../../snippets/intro_to_ios/readme_templates/prework_readme.md?raw=true).

**Note:** All project submissions happen via github. If needed, review how to push your apps to github with [this handy tutorial](https://help.github.com/articles/create-a-repo). You should add a [.gitignore](https://raw.githubusercontent.com/github/gitignore/master/Swift.gitignore) to your project as well to **avoid storing compiled and local files in git**.

### Submission Checklist

Please **review the following checklist** to ensure your submission is valid:
 * Did you implement both the **TipViewController** and the **SettingsViewController**?
 * Did you successfully **push your code to github**? Can you see the code on github?
 * Did you **add a README** which includes a **GIF walkthrough** of the app's functionality?
 * Did you **create an issue** on the repo and include **/cc @hao44le** in the issue body?

## 6. Extend your app, improve UI, add features

After initial submission, you should iterate by adding several additional features to your app. Engineers that submit apps with extended functionality and improved UI are **significantly more likely to be accepted** into the bootcamp. Try your hand at implementing the following user stories and any other extensions of your own choosing:

- Remember the bill amount across app restarts. After an extended period of time, clear the state. This is a UI trick that Apple uses with the Spotlight app. If you return there a minute later, it will show your most recent search. if you return 10 minutes later, it defaults to blank. To implement this, plug into the application lifecycle and track time using NSDate.
- Use locale specific currency and currency thousands separator.
- Add a light/dark color theme to the settings view. In viewWillAppear, update views with the correct theme colors.
- Make sure the keyboard is always visible and the bill amount is always the first responder. This way the user doesn't have to tap anywhere to use this app. Just launch the app and start typing.
- Add animations to your UI
- The Tip calculator has a very primitive UI. Feel free to play with colors, layout, or even modify the UI to improve it.

Please **reply back to the github issue** after pushing new features. Be sure to include in the README an updated GIF walkthrough using [LiceCap](http://www.cockos.com/licecap/).

# Excellent Past Submissions

Here are some examples of excellent past submissions. It only takes a couple hours to implement the basic version, but you should spend more time exploring the iOS framework.

<img src='https://github.com/hao44le/CS491-Tips/blob/master/Tips/Gelei_tips_demo.gif' title='img' width='200' alt='img' />
