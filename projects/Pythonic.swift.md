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

- @practicalswift (https://twitter.com/practicalswift, http://practicalswift.com/)

## Updates

### July 10

### July 25

### August 10

### August 25

### September 10

### September 25
