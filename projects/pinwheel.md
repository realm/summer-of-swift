# Pinwheel

## Description

I've just started working on a fully-functional Pinboard client for iPhone and iPad. This project aims to include a Safari / System Extension that will allow sending URLs from other apps to your Pinboard account.

I'm going lone-wolf on this one (the way I like it) and I aim to learn a great deal about Swift and the latest offerings provided by iOS 8. It'll include a full (and likely separate) API adapter for the Pinboard REST API as well (hopefully useable by others as a CocoaPod).

## Project Location

It lives at https://github.com/kreeger/pinwheel. GitHub Pages site is forthcoming, once I get the project far enough along.

## Team Members

- Ben Kreeger — @kreeger on [GitHub](https://github.com/kreeger), [Twitter](https://twitter.com/kreeger), ad nauseum

## Updates

### July 10

In starting this project off, I discovered a bit of a snag when working with the Keychain API in Swift. Below is a blog post I wrote about it.

---

So the Keychain is a nightmare and a half to work with in Swift right now. I'm trying to write a Keychain client using some code I've found on the Internet, but evidently I can't seem to get the hang of using these Foundation constants as keys in a Swift dictionary (to pass to `SecItemCopyMatching`). I've copied and pasted [the code from this post][mattpalmer], and the compiler gripes that there's no overload for `init` method for `NSDictionary` that accepts my supplied parameters. Evidently I'm [not the only one][so-me-too] with this problem, either.

``` swift
var keychainQuery: NSMutableDictionary = NSMutableDictionary(
    objects: [kSecClassGenericPassword, service, accountName, kCFBooleanTrue, kSecMatchLimitOne],
    forKeys: [kSecClass, kSecAttrService, kSecAttrAccount, kSecReturnData, kSecMatchLimit])
```

It seems to happen when I start mixing in my incoming `String` parameters into the dictionary's keys/values. What would be *awesome* is if I could use the dictionary syntax that Swift provides for us in order to build my Keychain query dictionary. That would require converting these Foundation constants into retained String values, right? Well, sadly that causes a nasty compiler error.

``` swift
var secClass: String = kSecClass.takeRetainedValue() as String
```

Barf.

```
Bitcast requires both operands to be pointer or neither
  %152 = bitcast %objc_object* %151 to %PSs9AnyObject_, !dbg !206
Bitcast requires both operands to be pointer or neither
  %153 = bitcast %PSs9AnyObject_ %152 to i8*, !dbg !206
LLVM ERROR: Broken function found, compilation aborted!
```

I've filed a bug report for this on Apple's bug reporter. Here's to hoping this gets fixed in Beta 3 (which I'm assuming is out Tuesday). I know I'm [not the only person to run into *this* issue][other-issue], either.

[mattpalmer]:    http://matthewpalmer.net/blog/2014/06/21/example-ios-keychain-swift-save-query/
[so-me-too]:     http://stackoverflow.com/questions/24453808
[other-issue]:     http://stackoverflow.com/questions/24145838

---

I'm working around the Keychain altogether for now and I'm starting to get a login view controller whipped up in code using Auto Layout. Once I get that settled, it's on to more API calls to Pinboard!

---

### July 25

### August 10

### August 25

### September 10

### September 25
