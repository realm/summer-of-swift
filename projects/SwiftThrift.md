# SwiftThrift

## Description

Implementation of Swift target language for the [Thrift](https://thrift.apache.org) message serialization library. This project consists of 2 parts:

- a runtime for message serialization and RPC, written in Swift
- a code generator for emitting Swift code from a Thrift IDL, written in C++

My intention is to get this merged into either Apache's Thrift project or Facebook's fork.

## Project Location

[https://github.com/klazuka/SwiftThrift]()

## Team Members

- Keith Lazuka - [@klazuka](https://github.com/klazuka)

## Updates

### July 13

Began work on the runtime, with a focus on basic message serialization/deserialization.

### July 27

The runtime is almost finished. You can serialize and deserialize all of the Thrift types, including collections and custom structs. The big remaining piece of functionality is to provide an RPC client transport (RPC server support is off-the-table for the initial release).

But I have also run into a few problems.

Swift's compiler-enforced initialization rules are great for type-safety, but it can also be too strict. For instance, when building up your object in a loop, the compiler errorneously reports that some of the properties were not initialized by the end of the init() function. I have filed a bug with Apple [http://openradar.appspot.com/radar?id=5866914186264576]().

This is a problem for my project because the input data for deserialization is unordered. So I need to loop through the data, filling-out properties as I go. In nearly all cases, the object will be completely initialized when the loop terminates, but the Swift compiler disagrees.

The other big problem which I have not yet solved is related to error-handling. Swift does not have exceptions, and yet Thrift itself uses exceptions heavily, both as values for failed RPC calls as well as for internal errors (e.g. buffer underflow). So far I'm just relying on assert() and fatalError(), but eventually I will need to make it work such that it does not crash the host application when an (inevitable) error is encountered.

In the RPC return value case, the solution should be pretty straightforward: define an enum with a success case and a case for each possible exception that the server may throw.

But I'm still undecided on how to handle internal errors such as buffer underflow or any other violation of the Thrift protocol.

Here's one thing that I learned: obtaining the IEEE-754 binary representation of a Double is actually just as easy to do in Swift as it is in c/c++/objc: all you need to do is call reinterpretCast(), binding the return value to a let/var of type UInt64. Given Swift's type-safety, I was worried that it was going to be very difficult to do. I'm glad that there's an escape hatch.


### August 9

Apple responded and closed my radar regarding false-positive compiler errors related to property assignment in Swift initializers.  I updated [my post on OpenRadar](http://openradar.appspot.com/radar?id=5866914186264576) to include Apple's response. The long and the short of it is that the Swift compiler conservatively rejects some valid programs when it cannot accurately determine whether a variable/property has been assigned at the end of the initializer. The workaround for my project is either going to be assigning a dummy value for each Thrift property when the property is defined or use a force-unwrapped optional. I still haven't decided.

I have spent quite a bit of time working on how to handle errors. The Thrift protocol heavily depends on the use of exceptions for both RPC application errors and Thrift runtime errors (such as buffer underflow and unexpected end-of-stream). But Swift does not allow you to throw or catch exceptions. Your choices are either crash the program (an acceptable choice for an app that you're writing, but not for a library) or return an error code whenever an error can be encountered.

I have chosen to take the latter route. But since an error can occur deep in the Thrift runtime, threading the error up the callstack results in a lot of boilerplate code to unpack, process and re-pack values. My solution is to introduce an enum that represents either an NSError or a generic value, and then use functional operators (like `fmap` and `bind`) to sequence/compose operations that may return an error. So far I've been experimenting with the error-handling in a separate project, but Landon Fuller has written-up a pretty good example that illustrates how something like this could be done in Swift for a [network client application](http://landonf.org/share/060714_149C1AF7_result-chaining.swift)

Unfortunately once you start to use Swift's enums in more powerful ways, you quickly run into compiler bugs. One big limitation is that enum's don't support generic types for their associated values, as described here: [http://owensd.io/2014/08/06/fixed-enum-layout.html](). That bug at least results in an assertion failure in the Swift compiler. But other bugs are more subtle: I wasted a couple hours this morning trying to figure out why the Swift compiler was maxing out my CPU in an infinite loop. Commenting and un-commenting large blocks of code eventually lead me to determine that the culprit was an infinite loop in the compiler when emitting the code for a switch statement case that includes a nested pattern with a typecast. I filed the bug with Apple and cross-posted to OpenRadar [http://openradar.appspot.com/radar?id=4978947682992128]().

There's still a lot left to do in my project. I never anticipated that the Swift language and compiler would be under such heavy development this summer. Just keeping pace with the language changes and working around compiler instability/bugs has been a challenge.

### August 25

### September 10

### September 25