# GatineauVélo

## Description

[https://github.com/philippec/GatineauVelo](This application) displays the bike paths for the City of Gatineau, and makes a good sample app for test-driven development. 

I'd like to rewrite it in Swift to compare and contrast, as the original was built in only a few hours.

## Project Location

### Work in progress:
	https://github.com/philippec/GatineauSwift

### Main project
	https://github.com/philippec/GatineauVelo
	
## Team Members

- Philippe Casgrain — @philippec


## Updates

### July 10

* Created project skeleton
* Created three base classes
* Created first real unit test (testCreation, woooo)

### July 25

* Took a different approach to the migration: trying one class at a time, with a hybrid ObjC-Swift project
* Successfully replaced an Objective-C class with an equivalent Swift class and it works!
* The unit test class, however, was not able to see the new Swift class because the Unit Test target lacked the generated header
* Filed rdar://problem/17851745 to address this issue

### August 10

### August 25

### September 10

### September 25
