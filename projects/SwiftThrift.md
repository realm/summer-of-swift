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

Swift's compiler-enforced initialization rules are great for type-safety, but it can also be too strict. For instance, when building up your object in a loop, the compiler errorneously reports that some of the properties were not initialized by the end of the init() function. I have filed a bug with Apple (http://openradar.appspot.com/radar?id=5866914186264576).

This is a problem for my project because the input data for deserialization is unordered. So I need to loop through the data, filling-out properties as I go. In nearly all cases, the object will be completely initialized when the loop terminates, but the Swift compiler disagrees.

The other big problem which I have not yet solved is related to error-handling. Swift does not have exceptions, and yet Thrift itself uses exceptions heavily, both as values for failed RPC calls as well as for internal errors (e.g. buffer underflow). So far I'm just relying on assert() and fatalError(), but eventually I will need to make it work such that it does not crash the host application when an (inevitable) error is encountered.

In the RPC return value case, the solution should be pretty straightforward: define an enum with a success case and a case for each possible exception that the server may throw.

But I'm still undecided on how to handle internal errors such as buffer underflow or any other violation of the Thrift protocol.

Here's one thing that I learned: obtaining the IEEE-754 binary representation of a Double is actually just as easy to do in Swift as it is in c/c++/objc: all you need to do is call reinterpretCast(), binding the return value to a let/var of type UInt64. Given Swift's type-safety, I was worried that it was going to be very difficult to do. I'm glad that there's an escape hatch.


### August 10

### August 25

### September 10

### September 25