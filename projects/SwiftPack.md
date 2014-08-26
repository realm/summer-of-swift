# SwiftPack

## Description

SwiftPack is a MsgPack library

## Project Location

https://github.com/briandw/SwiftPack

## Team Members

- Brian Williams — @briandw

## Updates

### July 12
Added the first version of the packer (the unpacker was already done)

### July 26
Fixed the packer and updated for the latest Xcode

### August 10
Switched to a Mac App. Updated to latest Xcode, but strings are still broken.

### August 17
Fixed the byte array to string conversion that Xcode-Beta5 broke. Using withUnsafeBufferPointer to access the byte pointer. Have a look at Unpacker -> stringFromSlice for an example.

### August 25
Updated for Xcode-Beta6. Removed stringFromSlice and replaced it with String.stringWithBytes(bytes: encoding:NSUTF8StringEncoding). Also switched the packer to using string.utf8 to get the string bytes.

### September 10

### September 25