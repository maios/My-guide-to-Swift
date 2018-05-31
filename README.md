# My-guide-to-Swift
Just a list of my Swift style preferences

# Swift Coding Convention

## Language

US English spelling to match with Apple's API. For example, consider using `color` instead of `colour`.

## Code organization

* Make liberal use of vertical whitespaces to divide code into logical chunks to improve the readibility.
* No trailing spaces. No leading indentation on empty lines.

### Use of extensions

Use extension to organize your code into logical blocks of functionality. Each extension should be set off with a `//MARK: ` comment to keep everything clear and well-organized.

#### Protocol conformance

Prefer adding a separate extension for the protocol conformance.

**Preferred**

```
class MyClass: MySuperClass {
	// Do something
}

extension MyClass: MyProtocol {
	// MyProtocol methods here
}
```

**Not Preferred**

```
class MyClass: MySuperClass, MyProtocol {
	// All methods
}
```

*Rationale: This keeps the related methods grouped together with the protocol and can simplify instructions to add a protocol to a class with its associated methods.
Read more at [Ray Wenderlich Swift Style Guide](https://github.com/raywenderlich/swift-style-guide#protocol-conformance)

### Use of `self`

Avoid explicit use of `self` except where required i.e inside a block or in lazy var definition or inside `@escape` closure. In short, if it works without `self`, then ditch it.

*Preferred*

```
func setupUI() {
	view.backgroundColor = UIColor.whiteColor()
}
```

*Not Preferred*

```
func setupUI() {
	self.view.backgroundColor = UIColor.whiteColor()
}
```

*Rationale: Omitting `self` allows for more concise code*

### Closures

**When calling a method with a single closure as the last argument, prefer to use trailing closures**

*Preferred*

```
// Swift 3.0
UIView.animate(withDuration: 0.5) {
	self.view.alpha = 0
}

// Swift 2.x
UIView.animateWithDuration(0.5) { 
	self.view.alpha = 0			
}
```

*Not Preferred*

```
UIView.animate(withDuration: animationDuration, animations: {
    self.myView.alpha = 0
})
```

*Rationale: By leaving the closure out of the parenthesis, it makes the code cleaner. This style also allows the creation of functions that look & feel like extensions of the Swift language itself.*

**When calling a method with multiple closure arguments, include all of the closures within the parenthesis with the clear name in the context of use**

*Preferred*

```
UIView.animate(withDuration: animationDuration, animations: {
        self.myView.alpha = 0
    },
    completion: { _ in
        self.myView.removeFromSuperview()
    }
)
```

*Not Preferred*

```
UIView.animate(withDuration: animationDuration, animations: {
        self.myView.alpha = 0
    }) { _ in
        self.myView.removeFromSuperview()
}
```

*Rationale: The `}) {` syntax can be very confusing, and this makes the code slightly less readable compared to passing both closures in with their named arguments. In general, we should simply prefer to avoid functions that take multiple closures, where possible.*


### Comments

* Comments should not be used to disable code. For unused (dead) code, delete it without fear or favor. [MAI]: It's oddly satisfying to me to delete unnecessary codes in our code base :-p
* Use comments to explain why a particular piece of code does something. Comments must be kept up-to-date or deleted.
* Avoid block comments inline with code, as the code should be as self-documenting as possible. Please add the links to JIRA ticket or link of problem explaination and fix if any.

### Import

Keep imports minimal.

* Whenever you are to add one import statement in the bridging header, make sure you don't duplicate it. 
* Whenever you are to add one import statement in a swift file, double check if it's really necessary. For example, `import UIKit` will become awkward when we've already had `import Foundation` which should be enough. [MAI] Xcode auto-completion tells us that `import UIKit` is useless anyway.

### Braces

* Opening braces should be placed consistently throughout the code base. In Zalora iOS, in-line opening braces is preferrable.
* Closing braces should always be on a new line by themselves, horizontally aligned with the left edge of the opening brace it is closing.

**Preferred**
```
if isHuman {
	// Do something
} else if isCat {
	knockEverythingOnTheTableDown()
}
```

**Not Preferred**
```
if isHuman
{
	// Do something
}
```

## Naming

* All names should make sense in the context of use.
* Type names (classes, structures, enums and protocols) should be upper camel case.
* Methods, variables and constants name should be lower camel case.
* Avoid use abbreviations and acronyms. Extremely common abbreviations such as URL are fine. Following the Apple Design Guidelines, abbreviations and initialisms that appear in all uppercase should be uniformly uppercase or lowercase (i.e URLString,urlString instead of uRlString).
* Following Apple's API Design Guidelines, protocols names that describe what something is should be a noun. Examples: Collection. Protocols names that describe an ability should end in -ing, -able, or -ible. Examples: Equatable, Resizing.
* Prefer function names to be in active voice, followed with parameters in context of use.
* Prefer having function/init methods named parameters for all arguments unless the context is super clear.
* Always keep up with Apple's documents about naming convention.

```
enum Weekday {
    case monday, tuesday, wednesday, thursday, friday
}

let itemsCount = 100

struct PrettyThing {
    var name: String
    var price: Double
}

class PrettyThingsCollectionViewController {
    func getPrettyThingsList() {...}
}
```

### Types

**Let Swift implicitly decide which type to use except when you require a type different from Swift's default inference.**

*Preferred*
```
let count = 10
let name = "Kim Jong Un"
let width: CGFloat = 10
```

*Not Preferred*
```
let count: Int = 10
let name: String = "Kim Jong Un"
```

*Rationale: Swift type inference system is well documented. Adding types where unnecessary adds clutter to the source code.*

**Prefer Swift native types to Objective-C's types.**

For example, `String` over `NSString`, etc

*Preferred*
```
var people: [Person] = [] // Array of Person
let count = 10 // Double
let widthString = (width as NSNumber).stringValue // String 
```

*Not Preferred*
```
var people = NSMutableArray()
let widthString: NSString = (width as NSNumber).stringValue // NSString
```

*Rationale: In the case of collections, preferring Swift-native types allows for more type-safety, one of the biggest advantages Swift has over Objective-C. In the case of other types, Swift is optimized for its native types. Almost (if not all) of the behavior is bridged across both types, and whenever you absolutely must have the Objective-C type, you can freely cast into it.*

### Constants

**Prefer to use `let` over `var`**

Prefer constants to variables. Use let as your default declaration keyword, and only change to var when you know the value of a variable will change.

*Preferred*
```
let maxRetryCount = 5
```

*Not Preferred*
```
var maxRetryCount = 5
```

*Rationale: Using let for variables which should not change can prevent bugs and keep the code's intent more clear.*

### Optionals

**Declare variables and function return types as optional with ? where a nil value is acceptable.**

The use of `!` should be avoided in general. If the return of a value could be nil i.e a function to access an array where the passed in index is out of bounds, declare a return type with `?`

*Preferred*
```
var phoneNumber: String?

func filterOtionAtIndex(index: Int) -> FilterOption? { ... }
```

*Not Preferred*
```
var phoneNumber: String!
```

*Rationale: Implicitly unwrapped optionals can lead to all sorts of problems and crashes. A little bit of extra unwrapping code is worth knowing that you won't see crashes from unwrapping nil.*

**Do not force unwrap optionals. If crashing is the correct behavior when the optional is `nil`, prefer a `fatalError` with a message.**

*Preferred*
```
guard let unwrapped = someOptional else {
    fatalError("Some helpful debugging message describing the circumstance.")
}
```

*Not Preferred*
```
let unwrapped = someOptional!
```

*Rationale: An extra line of code here can save plenty of headaches when debugging.*

**If a value should never be nil, prefer a non-optional.**

*Preferred*
```
class Customer {
	let email: String
}
```

*Not Preferred*
```
class Customer {
	let email: String? // email is mandatory when user is created, thus, can never be nil
}
```
*Rationale: Using optionals for values that really never should be nil makes code unclear. It can lead to bugs or crashes, and it makes it harder for other devs to understand your code. Also, using optionals has an added unwrapping overhead. Using optionals for values that should never be nil is usually a sign of problems elsewhere in the code.*

**When you need to unwrap an optional into a non-constant, prefer if var and guard var.**

*Preferred*
```
guard var volume = volume else {
	return
}
volume += 10

if var volume = volume else {
	volume += 10
}
```

*Not Preferred*
```
if let volume = volume {
	var vol = volume
	vol += 10
}
```

*Rationale: Using `if var` and `guard var` prevents muddying the current scope with unnecessary variable declarations.*

**When you need to unwrap a variable from a higher scope, prefer simply shadowing the variable name.**

*Preferred*
```
var imageURL: URL?

// later...
guard let imageURL = imageURL else { /*...*/ }
```

*Not Preferred*
```
var imageURL: URL?

// later...
guard let unwrappedImageURL = imageURL else { /*...*/ }
```

*Rationale: This prevents cluttering the current scope with more any more variables than we actually need, and makes the code slightly easier to read.*

**When unwrapping multiple variables, prefer the same if let or guard let clause.**

*Preferred*
```
if let person = people.first, profileImageURL = person.profileImageURL {
    // do something with profileImageURL
}
```

*Not Preferred*
```
if let person = people.first {
    if let profileImageURL = person.profileImageURL {
        // do something with profileImageURL
    }
}
```

*Rationale: This prevents unnecessary levels of nesting and helps avoid "arrow code".*

## Loops

**Prefer `for-in` loops to `forEach` in most circumstances.**

*Preferred*
```
for thing in things {
    thing.doTheThing()
}
```

*Not Preferred*
```
things.forEach { thing in
    thing.doTheThing()
}
```

*Rationale: The preferred style reads more naturally.*

**When dealing with optional collections, prefer `forEach` to unwrapping.**

*Preferred*
```
things?.forEach {
    thing.doTheThing()
}
```

*Not Preferred*
```
if let things = things {
    for thing in things {
        thing.doTheThing()
    }
}
```

*Rationale: While the `for-in` loop reads more naturally, using `forEach` to prevent unnecessary levels of nesting helps keep the overall code more readable.*

**When the loop body is nothing but passing each item in the loop into a closure, function, or method, prefer forEach.**

*Preferred*
```
things.forEach(handleTheThing)
```

*Not Preferred*
```
for thing in things {
    handleTheThing(thing)
}
```

*Rationale: This is more compact, and passing closure arguments into to methods that expect closures should feel perfectly natural in Swift.*

**Prefer functional `map | filter | reduce` to `for` loop in most circumstances**

*Preferred*
```
var events = [1...10].filter { $0 / 2 == 0 }
```

*Not Preferred*
```
var newEvens = [Int]()
for i in 1...10 {
	if i % 2 == 0 { 
		newEvens.append(i)
	 }
 }
````

*Rationale: Functional Programming is phenomenal. Functional Programming makes you look smart :-p*

## SwiftLint
