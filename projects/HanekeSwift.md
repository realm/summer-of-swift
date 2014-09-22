# Haneke

## Description

Haneke is a lightweight zero-config ~~image~~ generic cache for iOS. This a ~~port~~ complete redesign from Objective-C to Swift.

## Project Location

<https://github.com/hpique/HanekeSwift>

## Team Members

- Hermes Pique - Twitter: [@hpique][2] / GitHub: [@hpique][3]
- Luis Ascorbe - Twitter: [@lascorbe][3] / GitHub: [@lascorbe][4]
- Joan Romano - Twitter: [@joanromano][5] / GitHub: [@joanromano][6]
- Oriol Blanc - Twitter: [@oriolblanc][7] / GitHub: [@oriolblanc][8]

## Updates

### July 10

First approach of the specifications on what the finished project must look like.

### August 10

Getting a hang of the language and unit testing. 

The XCTest Swift API has improved greatly from Beta 1 to Beta 5. The XCTAssert macros were pratically unusable in Beta 1.

A couple techniques that we found interesting:

##### Testing deinit

    func testDeinit() {
        weak var sut = Something()
    }
    
##### Verifying that a function gets called

    func testUIApplicationDidReceiveMemoryWarningNotification() {
        let expectation = expectationWithDescription("onMemoryWarning")
        
        class MemoryCacheMock : MemoryCache {
            
            var expectation : XCTestExpectation?
            
            override func onMemoryWarning() {
                super.onMemoryWarning()
                expectation!.fulfill()
            }
        }
        
        let sut = MemoryCacheMock("test")
        sut.expectation = expectation // XCode crashes if we use the original expectation directly
        
        NSNotificationCenter.defaultCenter().postNotificationName(UIApplicationDidReceiveMemoryWarningNotification, object: nil)
        
        waitForExpectationsWithTimeout(0, nil)
    }

### August 25

We have an (almost) working LRU disk cache in Swift. Things we found interesting:

##### Using C libraries in framework projects

Apparently this has been negledted up to Beta 5. We want to use CommonCrypto to calculate a MD5 hash and we got it working by using module maps. However, this has two limitations:

* Using the custom framework in another project fails at compile time with the error missing required module 'CommonCrypto'. This is because the CommonCrypto module does not appear to be included with the custom framework. A workaround is to set the Import Paths in the project that uses the framework.
* The module map is not platform independent (it currently points to a specific platform, the iOS 8 Simulator). We don't know how to make the header path relative to the current platform.

More info: http://stackoverflow.com/questions/25248598/importing-commoncrypto-in-a-swift-framework

##### Golden path/early return with if let

Swift does not appear to be golden path/early return friendly, assuming you use `if let` as encouraged. For example:

    private func calculateSize() {
        let fileManager = NSFileManager.defaultManager()
        size = 0
        let cachePath = self.cachePath
        var error : NSError?
        if let contents = fileManager.contentsOfDirectoryAtPath(cachePath, error: &error) as? [String] {
            for pathComponent in contents {
                let path = cachePath.stringByAppendingPathComponent(pathComponent)
                if let attributes : NSDictionary = fileManager.attributesOfItemAtPath(path, error: &error) {
                    size += attributes.fileSize()
                } else {
                    NSLog("Failed to read file size of \(path) with error \(error!)");
                }
            }
        } else {
            NSLog("Failed to list directory with error \(error!)");
        }
    }

### September 10

We now have a fully functional LRU data disk cache and fully functiona memory+disk image cache. Now to polish and add features.

##### How to name block parameters

`*block` parameters didn't feel right in Swift. So instead of...

    public func fetchData(key : String, successBlock : (NSData) -> (), failureBlock : ((NSError?) -> ())? = nil)

...which is very ObjC-like. for starters, these are closures, not blocks. Currently we're doing...

    public func fetchData(key : String, success doSuccess : (NSData) -> (), failure doFailure : ((NSError?) -> ())? = nil)

...which is shorter for both the API user and us. This won't likely be the naming standard, but it beats using `block` in parameter names.

### September ~~25~~22

Last update! HanekeSwift is now a fully functional generic cache for iOS written in Swift. Not a port, but a complete redesign of the original Haneke that leverages Swift features such as generics to offer new functionality. Of course, there's a lot more work than can and will be done but the core functionaly is finished.

Here are some things that we learned in the last few days:

####Type inference quirks of closures as parameters

Swift can infer the type of closures when used as parameters. As shown in the official Swift [documentation](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Closures.html#//apple_ref/doc/uid/TP40014097-CH11-XID_152), this means that we don't need to write code like this:

```Swift
let reversed = sorted(names) { (s1: String, s2: String) -> Bool in
    return s1 > s2
}
```

Instead, we can simply write:

```Swift
let reversed = sorted(names) { s1, s2 in
    return s1 > s2
}
```

Because single-expression closures can implicitly return the value of the expression, the code above can be shortened to:

```Swift
let reversed = sorted(names) { s1, s2 in
    s1 > s2
}
```

Or even more succintly by using shorthand argument names:

```Swift
let reversed = sorted(names) { $0 > $1 }
```

So far so good. However, the documentation leaves out some cases in which Swift fails to infer the closure type properly (as of Xcode 6.0). During the development of [Haneke](https://github.com/Haneke/HanekeSwift) we discovered the two below. You can follow along the code examples with [this playground](https://github.com/hpique/Articles/tree/master/Swift/Type%20inference%20quirks%20of%20closure%20parameters/Type%20inference%20quirks%20of%20closure%20parameters.playground).

##### Quirk 1: Single-expression closures with unused return value

The first involves single-expression closures. Say we want to set the modification date of a file in background, without much care for errors. Intuitively, we would write something like this:

```Swift
dispatch_async(someBackgroundQueue) {
    NSFileManager.defaultManager().setAttributes([NSFileModificationDate : someDate], ofItemAtPath: path, error: nil)
}
```

Unfortunately the above code fails to compile with error `Cannot convert the expression's type '(dispatch_queue_t!, () -> () -> $T5)' to type 'Bool'`.

The reason is that `NSFileManager.setAttributes` returns a `Bool`, and because the closure has a single-expression, the Swift compiler mistakenly infers that its return type is `Bool`. 

The workaround? We guide the Swift compiler by ignoring the result value with an underscore:

```Swift
dispatch_async(someBackgroundQueue) {
    let _ = NSFileManager.defaultManager().setAttributes([NSFileModificationDate : someDate], ofItemAtPath: path, error: nil)
}
```

##### Quirk 2: Unused parameters in closures

Another quirk involves unused parameters in closures. Consider a function that fetches some data and can succeed or fail.

```Swift
func fetchDataWithSuccess(success doSuccess : (NSData) -> (), failure doFailure : ((NSError?) -> ()))
```

When writing unit tests for this function we should test both the success case and the failure case. For the failure case, we should fail the test if the success closure gets called. The test could look something like this:

```Swift
func testFailure() {
    // Simulate a failure condition

    let expectation = self.expectationWithDescription("fetch")
    fetchDataWithSuccess(success: {
        XCTFail("expected failure")
        expectation.fulfill()
    }, failure : { e in
        XCTAssertNotNil(e)
        expectation.fulfill()
    })
    self.waitForExpectationsWithTimeout(1, handler: nil)
}
```

Surprisingly this doesn't compile. The error message is not very helpful: `'NSData' is not a subtype of '()'`.

The problem lies in the unused parameter of the success block. The Swift compiler assumes that the success closure does not have any parameters, and fails to match this with the expected type of the success closure.

What to do? Again the amazing underscore comes to our rescue:

```Swift
func testFailure() {
    // Simulate a failure condition

    let expectation = self.expectationWithDescription("fetch")
    fetchDataWithSuccess(success: {let _ in
        XCTFail("expected failure")
        expectation.fulfill()
    }, failure : { e in
        XCTAssertNotNil(e)
        expectation.fulfill()
    })
    self.waitForExpectationsWithTimeout(1, handler: nil)
}
```

Telling Swift that the success closure has a parameter, even if we ignore it, is enough help to let it infer the type correctly.


[1]: https://twitter.com/hpique
[2]: https://github.com/hpique
[3]: https://twitter.com/lascorbe
[4]: https://github.com/lascorbe
[5]: https://twitter.com/joanromano
[6]: https://github.com/joanromano
[7]: https://twitter.com/oriolblanc
[8]: https://github.com/oriolblanc
