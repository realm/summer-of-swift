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
