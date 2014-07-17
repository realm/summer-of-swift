# Pythonic.swift

## Description

Pythonic.swift is a Swift library implementating selected parts of Python's standard library and making them available to your Swift code.

```import Pythonic``` allows you to write Python flavored code such as:

```
#!/usr/bin/env xcrun swift -i -I .

import Pythonic

var strings = ["foo", "bar"]
if strings {
  println(strings[0]) // foo
}
if len(strings) == 2 {
  println(strings[1].upper()) // BAR
}

var greeting = "   hello pythonista   "
if greeting.strip().startswith("hello") {
  println(greeting.strip().title()) // Hello Pythonista
}

var numbers = [1, 2, 3, 4, 5]
println(sum(numbers)) // 15
println(max(numbers)) // 5
```

## Project Location

https://github.com/practicalswift/Pythonic.swift

## Team Members

- <a href="https://github.com/practicalswift">@practicalswift</a> (https://twitter.com/practicalswift, http://practicalswift.com/)

## Updates

### July 10

Continued implementing functions from the Python standard library in Swift.

Posted a "Show HN" on the project which reached the first page of HN for a couple of hours (https://news.ycombinator.com/item?id=8001245).

Learned about the smart `GeneratorOf<T>` class, which allows for easy creation of generators which are needed when implementing the `Sequence` protocol.

A Sequence allows for the `for x in [sequence]` in Swift. This is an example of a `generate()` function using `GeneratorOf<T>`.

```
    func generate() -> GeneratorOf<String> {
        var i = 0
        var lines = self.readLines().map { $0 + "\n" }
        return GeneratorOf<String> {
            if i >= len(lines) {
                return .None
            } else {
                return lines[i++]
            }
        }
    }
```

Call for contributors: Please join me in implementing the Python standard library in Swift. It is really fun, and you'll learn a lot about Swift during the process! :-)

### July 25

### August 10

### August 25

### September 10

### September 25
