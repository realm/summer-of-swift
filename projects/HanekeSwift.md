# Haneke

## Description

Haneke is a lightweight zero-config image cache for iOS. This a port from Objective-C to Swift.

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

### September 25

[1]: https://twitter.com/hpique
[2]: https://github.com/hpique
[3]: https://twitter.com/lascorbe
[4]: https://github.com/lascorbe
[5]: https://twitter.com/joanromano
[6]: https://github.com/joanromano
[7]: https://twitter.com/oriolblanc
[8]: https://github.com/oriolblanc
