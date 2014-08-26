# The Oakland Post

## Description

The mobile companion to [The Oakland Post](http://www.oaklandpostonline.com)!

Contributions are more than welcome! :grin::thumbsup:

## Project Location

https://github.com/aclissold/The-Oakland-Post

## Team Members

- [@aclissold](https://github.com/aclissold) ([andrewclissold.appspot.com](http://andrewclissold.appspot.com))

## Updates

### July 10

Open-sourced the project and added screenshots!

### July 25

A grand total of
[41 commits](https://github.com/aclissold/The-Oakland-Post/commits/3bddf0ff25c6d1f3d8ba01e8439f4088960fb103) and
[19 closed issues](https://github.com/aclissold/The-Oakland-Post/issues?q=closed%3A%3C2014-07-25)
since the 10th.

The majority of what I've been working on these past couple weeks has been this
project, but three interesting micro-ideas arose from getting to play with Swift
in this project:

#### BugFixTableViewController

[BugFixTableViewController](https://github.com/aclissold/BugFixTableViewController)
is a tiny subclass of `UITableViewController` that fixes a small but very
annoying bug that's been around since at least iOS 7 (meaning it's too late for
it to be fixed in the SDKs). Just swap out your UITableViewControllers with
BugFixTableViewControllers and you're good to go!

#### GCD.swift

[GCD.swift](https://github.com/aclissold/The-Oakland-Post/blob/365c491/The%20Oakland%20Post/GCD.swift)
is a super thin wrapper around some dispatch_async functions. I *love* Swift's
last-parameter closure syntax—it makes this possible:

``` swift
onMain {
    // perform some UI task on the main queue
}
```

#### maybe.swift

I wondered if you could extend the builtin `Bool` to consist of not only
`Bool.true` and `Bool.false` but also a `Bool.maybe`. So I came up with a way to
do that in a Playground and stuck it in a `.swift` file. Here's how it looked:

``` swift
import Darwin

extension Bool {
    static var maybe: Bool {
        return arc4random() < (UInt32.max / 2)
    }
}
```

 I also included a `var maybe` with a computed getter returning `Bool.maybe`,
 since it's a value type and needs to return a new value each time it's
 accessed.

However! Xcode 6 Beta 4 came along and made `true` and `false` literals in
the grammar, nerfing the entire idea of a "maybe" living right alongside true
and false. It was fun while it lasted! Plus, when there's three values it's technically
not Boolean anymore :stuck_out_tongue:

#### The Oakland Post

As for my actual Summer of Swift project, I made a lot of great progress on it:

* Implemented the core functionality of each of the main components
    * A **Home** view for the main newsfeed
    * **Sections** for newsfeeds of various categories
    * **Photos**, a dynamically arranged collection of images
    * **Blogs**, another newsfeed for an important category on the website
* Decided on Avenir Next as the theme sans serif font and Palatino as the serif
* Figured out how to load a fullscreen, high-res image when one of the Photos is tapped
* Created a TopScrollable protocol for VCs that should scroll to the top when
  their tab bar item is tapped
* Made a lot of UI improvements throughout

There are just two main issues I ran into.

1. `dismissModalViewControllerAnimated` was working just fine, but when Swift
became unable to use deprecated APIs, I switched that method call to
`dismissViewControllerAnimated` instead... but this keeps giving me an
`EXC_BAD_ACCESS` for some reason and I can't for the life of me figure out why.
I enabled zombie objects, and it looks like one of the autolayout objects (an
`NSLayoutGuide`) is being accessed after its container view is dismissed and
deallocated... but I'm really not sure. I couldn't find any solutions online so
filed it as a bug, since I would expect swapping methods like that to just work.

2. I'm displaying post content in a UIWebView, and using a couple lines of
JavaScript to change the font and hide a header. However, this doesn't get
executed until the content is fully loaded and visible, so I'm starting the web
view out invisible and showing it after executing the JavaScript. Unfortunately,
`stringByEvaluatingJavaScriptFromString(_:)` seems to be asynchronous, so I had
to fudge a delay before showing the web view. If anybody knows of a better
solution to this, please let me know! I found that the analogous method in
`WKWebView` lets you pass a callback block (exactly what I want), but I'm targeting iOS 7+.

### August 10

I'm headed off to Europe tomorrow so I'm submitting this early but counting it
as an August 10th update!

There are
[25 new commits](https://github.com/aclissold/The-Oakland-Post/commits/5cba8530c00c4d9023634a834abd17c069949e8c) and
[5 closed issues](https://github.com/aclissold/The-Oakland-Post/issues?q=closed%3A2014-07-25..2014-08-05)
since last time.

The vast majority of this work went towards whipping the Photos view into shape:

![Screenshot 2](https://raw.githubusercontent.com/aclissold/The-Oakland-Post/5cba8530c00c4d9023634a834abd17c069949e8c/The%20Oakland%20Post/Screenshots/Screenshot%202.png)

You can now tap any image in the collection view and a full-screen version will
fade in. A progress circle is displayed as
[XPaths](http://en.wikipedia.org/wiki/XPath) and the
[Open Graph protocol](http://ogp.me/) are used find to download a
high-res version of the image from the server, which is then seamlessly swapped
out with the lower-res version, even when you navigate back to the collection
view. The
[`EnlargedPhoto`](https://github.com/aclissold/The-Oakland-Post/blob/5cba8530c00c4d9023634a834abd17c069949e8c/The%20Oakland%20Post/EnlargedPhoto.swift)
can be dismissed with a swipe or single tap, and zoomed with a pinch or double
tap. And each image now provides a link to view the associated news post, which
gets presented modally.

### August 25

Although I've been in Europe between these two updates (just
[2 closed issues](https://github.com/aclissold/The-Oakland-Post/issues?q=closed%3A2014-08-05..2014-08-25)),
I did manage to make
[23 commits](https://github.com/aclissold/The-Oakland-Post/commits/1a2e3f07ced2cabbde01f6d24ec4fe410d6e8c4a)
in the past couple days. I fixed quite a few crashes, created
[p.swift](https://github.com/aclissold/The-Oakland-Post/blob/9d26421d5b8553a19bc5916305a10f874b6ba488/The%20Oakland%20Post/p.swift)
as an alias for `println()`, and created new Login and Sign Up views.

But perhaps most importantly, I decided on [Parse](https://parse.com/) as a way
to persist favorited Posts. I'd never heard of it before this week, but its
ease-of-use is blowing my mind:

``` swift
let username = usernameTextField.text
let password = passwordTextField.text
PFUser.logInWithUsernameInBackground(username, password: password) {
    // do stuff with the PFUser or NSError arguments to this callback closure
}
```

is how simple it is to log in a user on the server! And to think I considered
writing my own server and networking code. That would have been overkill for
the scope of this project, and Parse looks like it'll do exactly what I need it
to.

Other than during the [MHacks](http://mhacks.org/) hackathon September 5th-7th,
I'm expecting to lay down the law on this project these next two weeks, so stay
tuned!

### September 10

### September 25
