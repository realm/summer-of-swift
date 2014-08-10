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

Most of my efforts lately have focused around JSON parsing and exploring how Swift's enums work. Here's my blog post on the subject.

---

In my recent work on [Pinwheel](https://github.com/kreeger/pinwheel) whenever I have the time (heh), I've gotten used to some of the more interesting behaviors and idioms of Swift. While I still seem to have [issues accessing the keychain](/swift-related-keychain-woes) in a sane manner, I finally experienced somewhat of a "eureka" moment when trying to parse a JSON response from the Pinboard API, but not after some frustration and face-palming.

I stubled upon [SwiftyJSON][gh-repo] after looking at a [series of][blog1] [blog posts][blog2] by David Owens II (as I assume many Swift early explorers have), and cracked open [the main source file][swifty] and my brain proceeded to melt. *None of it made much sense to me*, and I quickly realized that it's because the `JSONValue` class was implemented as an `enum` type. I'm familiar with enum types in other languages, especially Objective-C, so when I saw all this other extra stuff sprinkled in (including *methods*? *calculated properties*? *switch statements*? *what*?), I couldn't tell if I was looking at a `class` or something else.

Determined to figure this thing out (since obviously other people have as well), I turned to Apple's rather thorough [documentation on Swift enumerations][enum-doc], and after multiple read-throughs, I picked out a couple of sections that were extremely relevant to understanding the code at hand:

- [Matching Enumeration Values with a Switch Statement][enum-doc1]
- [Associated Values][enum-doc2]

These two features combined make Swift `enums` somewhat of a force to be reckoned with. I'm still getting a handle on just what exactly these are fully capable of (hint: a lot), but with those concepts combined, a class like SwiftyJSON's `JSONValue` are possible, and here's how.

## Enum initialization

You can create an initializer on your custom enum. Note that there isn't one provided for you by default, but when you do add one, you'll need to make sure that by the time you `return self` from the function that you've assigned one of the `enum` cases to `self`. This is how SwiftyJSON embraces this capability (reformatted for brevity; [see the full source][sj-init]), in two phases (the first utilizes the second).

``` swift
init (_ data: NSData!) {
    if let value = data {
        var error: NSError? = nil
        if let jsonObject: AnyObject = NSJSONSerialization.JSONObjectWithData(data, options: nil, error: &error) {
            self = JSONValue(jsonObject)
        } else {
            self = JSONValue.JInvalid(jsonError)
        }
    } else {
        self = JSONValue.JInvalid(dataError)
    }
}
```

If de-serialization fails for whatever reason, there's a `JInvalid` case that serves as an error state for the enumeration, to ensure there's still a state assigned to `self` by the time the initializer finishes. If data de-serialization succeeds, then the resulting `NSDictionary` is passed to the *other* initializer, which is shown below. Note that this example is also truncated for brevity, so please [look at the full source][sj-init1] for a better picture.

``` swift
init (_ rawObject: AnyObject) {
    switch rawObject {
    case let value as NSNumber:
        if String.fromCString(value.objCType) == "c" {
            self = .JBool(value.boolValue)
            return
        }
        self = .JNumber(value)
    case let value as NSString:
        self = .JString(value)
    case let value as NSNull:
        self = .JNull
    // So on and so forth, for arrays, dictionaries, etc.
}
```

Each of these cases work as a type check, and if one succeeds, the "type case" (either `JBool`, `JString`, `JArray`, etc.) is assigned to self, and the actual typed data is assigned to the instance/case of the enum as an "associated value."

## Associated value handling

Each of these "type cases" are defined at [the top of the class][sj-cases].

``` swift
case JNumber(NSNumber)
case JString(String)
case JBool(Bool)
case JNull
case JArray(Array<JSONValue>)
case JObject(Dictionary<String,JSONValue>)
case JInvalid(NSError)
```

Depending on the case chosen in the initializer, the instance of the enum gets handed the value of the specific type. Note that `JNumber` gets handed an `NSNumber`, because this more generic number class handles both `Int` and `Double` number types; those are pulled out of the enumeration below. Take a look at [the property accessor for the `integer` property][sj-property]:

``` swift
var integer: Int? {
    switch self {
    case .JBool(let value):
        return Int(value)
    case .JNumber(let value):
        return value.integerValue
    case .JString(let value):
        return (value as NSString).integerValue
    default:
        return nil
    }
}
```

This funky syntax is what initially threw me for a loop. `case .JBool(let value)`? What's the `let value` doing in *there*? [Apple's documentation on Associated Values][enum-doc2] explains this part.

> ...The associated values can be extracted as part of the switch statement. You extract each associated value as a constant (with the let prefix) or a variable (with the var prefix) for use within the switch case’s body.

The switch statement in Swift is defined in such a way that we can *check the associated value* by assigning to it in a syntax that *looks* as if you're "reverse assigning" the case, or something.

## Putting it all together

So using the recursive initializer that assigns itself cases based on type, plus assigning associated values to each instance of the enum, provides a pretty powerful way to turn a big JSON object into a easily-useable "object-like" object. Here's how I'm using it in a part of [Pinwheel][pw-link].

``` swift
var parsed = [PinboardPost]()
for post in result["posts"].array! {
    parsed.append(PinboardPost(responseDict: post))
}
```

It's pretty easy to turn my `result` instance, which is a `JSONValue`-wrapped object, into a powerful, traversable object. Then I can pluck out values relatively easily in my object initializer as well.

``` swift
class PinboardPost {
    let title: String
    var extendedDescription: String?
    let hash: String
    let href: NSURL
    // ...
    
    init(responseDict: JSONValue) {
        self.title = responseDict["description"].string!
        self.extendedDescription = responseDict["extended"].string
        self.hash = responseDict["hash"].string!
        self.href = responseDict["href"].url!
        // ...
    }
}
```

Big thanks go to [Ruoyu Fu][lingoer] and his [SwiftyJSON][gh-repo] library; it's been a tremendous help in learning and utilizing Swift!

[blog1]:     https://medium.com/swift-programming/b6f4f232e35e
[blog2]:     https://medium.com/swift-programming/swift-json-parsing-716ea9be1c5b
[swifty]:     https://github.com/lingoer/SwiftyJSON/blob/master/SwiftyJSON/SwiftyJSON.swift
[gh-repo]:     https://github.com/lingoer/SwiftyJSON
[enum-doc]:     https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Enumerations.html
[enum-doc1]:     https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Enumerations.html#//apple_ref/doc/uid/TP40014097-CH12-XID_186
[enum-doc2]:     https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Enumerations.html#//apple_ref/doc/uid/TP40014097-CH12-XID_187
[sj-init]:     https://github.com/lingoer/SwiftyJSON/blob/85703f67fa39730dceae2a1c7813242ee5a8269c/SwiftyJSON/SwiftyJSON.swift#L142
[sj-init1]:     https://github.com/lingoer/SwiftyJSON/blob/85703f67fa39730dceae2a1c7813242ee5a8269c/SwiftyJSON/SwiftyJSON.swift#L156
[sj-cases]:     https://github.com/lingoer/SwiftyJSON/blob/85703f67fa39730dceae2a1c7813242ee5a8269c/SwiftyJSON/SwiftyJSON.swift#L29
[sj-property]:    https://github.com/lingoer/SwiftyJSON/blob/85703f67fa39730dceae2a1c7813242ee5a8269c/SwiftyJSON/SwiftyJSON.swift#L74
[pw-link]:    https://github.com/kreeger/pinwheel
[lingoer]:    https://github.com/lingoer

---

Now it's back to work on more API components. Progress was slow this last week but I aim to pick things up again at a better pace next week.

---

### August 10

In getting to crack Pinwheel open again, naturally everything's all kinds of broken again. Evidently the few changes I had to make include...

- Swapping out some of my useages of `+=` with `.append()` instead
- Adding `required init(code aDecoder: NSCoder) {}` initializers to my UIView(Controller) subclasses
- Evaluating `<bool-name> != nil` instead of just `<bool-name>` in a few places

As far as the "state of the union as I see it," Swift is still incredibly frustrating to work with under Xcode (the latest beta being Xcode 6 Beta 5) due to the SourceKitService dying out constantly. Smart highlighting and code suggestions still just go *right* out the window. The way I see it? September's right around the corner. I really hope they can get these bugs ironed out before then, because I would like to start using Swift in a professional capacity instead of just on personal projects. Until the Xcode/SourceKit bugs are fixed, I can't see myself doing that.

In either case, I had some Pinboard API authentication issues, and they were caused by some errant Swift code that tried to modify an incoming variable. It *used* to work, and I was pretty dubious then about whether it would continue to work as Apple refined the Swift compiler. Turns out I was right.

For a positive spin on things, Xcode finally supports grepping for `// MARK` and `// TODO` so that they'll show up in the symbol navigator. I'm using them now in the code for my Pinboard API client (where I'm utilizing extensions to separate post retrieval vs other calls).

![Symbol Navigator](http://f.kree.gr/blog/2014/symbol-menu.png)

In addition to these, I also get pretty nerdy about docstrings. I like to include them wherever possible. In previous betas, Xcode's built-in parsing was much like pulling teeth, but since Beta 5 came out, support for these is better than ever (and a tip of the hat to [NSHipster's post on the subject a couple of weeks ago](http://nshipster.com/swift-documentation/), which even sheds light on a rudimentary reStructuredText parser built into SourceKit).

```swift
// MARK: User authentication
extension PinboardClient {
    /**
    Trades a username and password for a login token, which is assigned to this adapter's
    credentials property.
    
    See https://pinboard.in/api#user_api_token
    
    :param: username The username to deliver to Pinboard's API.
    :param: password The password to deliver to Pinboard's API; is never stored.
    :param: completion A block to be called upon completion.
    */
    func login(#username: String, password: String, completion: ((PinboardCredentials?, NSError?) -> Void)) {
        // ...
    }
}
```

![Parsed Comments](http://f.kree.gr/blog/2014/parsed-comments.png)

Apart from these discoveries and minor frustrations, I'm still plugging away (slowly) at Pinwheel. Eventually a basic version will see the light of day!

### August 25

### September 10

### September 25
