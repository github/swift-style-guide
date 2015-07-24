#Intrepid Pursuits
##Swift Style Guide

This is the initial documentation of Intrepid best practices and style regarding the Swift language.

###Making / Requesting Changes

Feedback and change are encouraged!  The current maintainers of this style guide are @brightredchili and @loganwright.  If you have an issue with any of the existing rules and would like to see something changed, please see the [contribution guidelines](CONTRIBUTING.md),
then open a pull request. :zap:

###Goals

This is an attempt to encourage patterns that accomplish the following goals (in
rough priority order):

 1. Increased rigor, and decreased likelihood of programmer error
 1. Increased clarity of intent
 1. Aesthetic consistency

###Table Of Contents

* [Whitespace](#whitespace)
* [Code Grouping](#code-crouping)
* [Let vs Var](#let-vs-var)
* [Force-Unwrapping of Optionals](#force-unwrapping-of-optionals)
* [Implicitly Unwrapped Optionals](#implicitly-unwrapped-optionals)
* [Getters](#getters)
* [Access Control](#access-control)
* [Type Specifications](#type-specification)
* [Referring to self](#referring-to-self)
* [Structs vs Classes](#structs-vs-classes)

####Whitespace

 * Tabs, not spaces.
 * End files with a newline.
 * Make liberal use of vertical whitespace to divide code into logical chunks.
 * Don’t leave trailing whitespace.
   * Not even leading indentation on blank lines.

####Code Grouping

Code should strive to be separated into meaningful chunks of functionality.  These larger chunks should be indicated by using the `// MARK: ` keyword.

When grouping protocol conformance, always use the name of the protocol and only the name of the protocol

**For Example:**

```Swift
// MARK: UITableViewDelegate
```

**Not**

```Swift
// MARK: UITableViewDelegate Methods
```

-- or --

```Swift
// MARK: Table View Delegate
```

####Let vs Var

Prefer `let`-bindings over `var`-bindings wherever possible

Use `let foo = …` over `var foo = …` wherever possible (and when in doubt). Only use `var` if you absolutely have to (i.e. you *know* that the value might change, e.g. when using the `weak` storage modifier).

_Rationale:_ The intent and meaning of both keywords is clear, but *let-by-default* results in safer and clearer code.

A `let`-binding guarantees and *clearly signals to the programmer* that its value is supposed to and will never change. Subsequent code can thus make stronger assumptions about its usage.

It becomes easier to reason about code. Had you used `var` while still making the assumption that the value never changed, you would have to manually check that.

Accordingly, whenever you see a `var` identifier being used, assume that it will change and ask yourself why.

#### Force-Unwrapping of Optionals

If you have an identifier `foo` of type `FooType?` or `FooType!`, don't force-unwrap it to get to the underlying value (`foo!`) if possible.

Instead, prefer this:

```swift
if let foo = foo {
    // Use unwrapped `foo` value in here
} else {
    // If appropriate, handle the case where the optional is nil
}
```

When dealing with `weak` or optional delegates, optional binding syntax is always preferable unless functionality is dependent on that value being unwrapped.

**For Example:**

```Swift
delegate?.doSomething(self)
```

**Not**

```Swift
if let delegate = self.delegate {
  delegate.doSomething(self)
}
```

_Rationale:_ Explicit `if let`-binding of optionals results in safer code. Force unwrapping is more prone to lead to runtime crashes.

####Implicitly Unwrapped Optionals

Implicitly unwrapped optionals have the potential to cause runtime crashes and should be used carefully. If a variable has the possibility of being `nil`, you should always declare it as an optional `?`.

Implicitly unwrapped optionals may be used in situations where limitations prevent the use of a non-optional type, but will never be accessed without a value.

If a variable is dependent on `self` and thus not settable during initialization, consider using a `lazy` variable.

```Swift
lazy var customObject: CustomObject = CustomObject(dataSource: self)
```

_Rationale:_ Explicit optionals result in safer code. Implicitly unwrapped optionals have the potential of crashing at runtime.

####Getters

When possible, omit the `get` keyword on read-only computed properties and
read-only subscripts.

So, write these:

```swift
var myGreatProperty: Int {
	return 4
}

subscript(index: Int) -> T {
    return objects[index]
}
```

… not these:

```swift
var myGreatProperty: Int {
	get {
		return 4
	}
}

subscript(index: Int) -> T {
    get {
        return objects[index]
    }
}
```

_Rationale:_ The intent and meaning of the first version is clear, and results in less code.

####Access Control

Top-level functions, types, and variables should always have explicit access control specifiers:

```swift
public var whoopsGlobalState: Int
internal struct TheFez {}
private func doTheThings(things: [Thing]) {}
```

However, definitions within those can leave access control implicit, where appropriate:

```swift
internal struct TheFez {
	var owner: Person = Joshaber()
}
```

When dealing with functionality that relies on ObjC systems such as the target-selector pattern, one should still strive for appropriate access control.  This can be achieved through the `@objC` attribute.  

**For Example:**

```Swift
@objc private func handleTap(tap: UITapGestureRecognizer)
```

**Not**

```Swift
public func handleTap(tap: UITapGestureRecognizer)
```

_Rationale:_ It's rarely appropriate for top-level definitions to be specifically `internal`, and being explicit ensures that careful thought goes into that decision. Within a definition, reusing the same access control specifier is just duplicative, and the default is usually reasonable.

####Type Specifications

When specifying the type of an identifier, always put the colon immediately
after the identifier, followed by a space and then the type name.

```swift
class SmallBatchSustainableFairtrade: Coffee { ... }

let timeToCoffee: NSTimeInterval = 2

func makeCoffee(type: CoffeeType) -> Coffee { ... }

func swap<T: Swappable>(inout a: T, inout b: T) { ... }
```

#####Closure Specifications

It is preferable to associate a closure's type from the left hand side when possible.

 **For example:**

 ```Swift
 let layout: (UIView, UIView) -> Void = { view1, view2 in
   view1.center = view2.center
   // ...
 }
 ```

 **Not**

```Swift
let layout = { (view1: UIView, view2: UIView) in
  view1.center = view2.center
  // ...
}
```

#####Type Inference

Unless it impairs readability or understanding, it preferable to rely on Swift's type inference where appropriate.

**For example:**

```Swift
let hello = "Hello"
```

**Not**

```Swift
let hello: String = "Hello"
```

This does not mean one should avoid those situations where an explicit type is required.

**For example:**

```Swift
let padding: CGFloat = 20
var hello: String? = "Hello"
```

_Rationale:_ The type specifier is saying something about the _identifier_ so
it should be positioned with it.

Also, when specifying the type of a dictionary, always put the colon immediately
after the key type, followed by a space and then the value type.

```swift
let capitals: [Country: City] = [ Sweden: Stockholm ]
```

####Referring to `self`

When accessing properties or methods on `self`, leave the reference to `self` implicit by default:

```swift
private class History {
	var events: [Event]

	func rewrite() {
		events = []
	}
}
```

Only include the explicit keyword when required by the language—for example, in a closure, or when parameter names conflict:

```swift
extension History {
	init(events: [Event]) {
		self.events = events
	}

	var whenVictorious: () -> () {
		return {
			self.rewrite()
		}
	}
}
```

_Rationale:_ This makes the capturing semantics of `self` stand out more in closures, and avoids verbosity elsewhere.

####Structs vs Classes

Unless you require functionality that can only be provided by a class (like identity or deinitializers), implement a struct instead.

Note that inheritance is (by itself) usually _not_ a good reason to use classes, because polymorphism can be provided by protocols, and implementation reuse can be provided through composition.

For example, this class hierarchy:

```swift
class Vehicle {
    let numberOfWheels: Int

    init(numberOfWheels: Int) {
        self.numberOfWheels = numberOfWheels
    }

    func maximumTotalTirePressure(pressurePerWheel: Float) -> Float {
        return pressurePerWheel * Float(numberOfWheels)
    }
}

class Bicycle: Vehicle {
    init() {
        super.init(numberOfWheels: 2)
    }
}

class Car: Vehicle {
    init() {
        super.init(numberOfWheels: 4)
    }
}
```

could be refactored into these definitions:

```swift
protocol Vehicle {
    var numberOfWheels: Int { get }
}

extension Vehicle {
    func maximumTotalTirePressure(pressurePerWheel: Float) -> Float {
        return pressurePerWheel * Float(numberOfWheels)
    }  
}

struct Bicycle: Vehicle {
    let numberOfWheels = 2
}

struct Car: Vehicle {
    let numberOfWheels = 4
}
```

_Rationale:_ Value types are simpler, easier to reason about, and behave as expected with the `let` keyword.

#### Make classes `final` by default

Classes should start as `final`, and only be changed to allow subclassing if a valid need for inheritance has been identified. Even in that case, as many definitions as possible _within_ the class should be `final` as well, following the same rules.

_Rationale:_ Composition is usually preferable to inheritance, and opting _in_ to inheritance hopefully means that more thought will be put into the decision.


#### Omit type parameters where possible

Methods of parameterized types can omit type parameters on the receiving type when they’re identical to the receiver’s. For example:

```swift
struct Composite<T> {
	…
	func compose(other: Composite<T>) -> Composite<T> {
		return Composite<T>(self, other)
	}
}
```

could be rendered as:

```swift
struct Composite<T> {
	…
	func compose(other: Composite) -> Composite {
		return Composite(self, other)
	}
}
```

_Rationale:_ Omitting redundant type parameters clarifies the intent, and makes it obvious by contrast when the returned type takes different type parameters.

#### Use whitespace around operator definitions

Use whitespace around operators when defining them. Instead of:

```swift
func <|(lhs: Int, rhs: Int) -> Int
func <|<<A>(lhs: A, rhs: A) -> A
```

write:

```swift
func <| (lhs: Int, rhs: Int) -> Int
func <|< <A>(lhs: A, rhs: A) -> A
```

_Rationale:_ Operators consist of punctuation characters, which can make them difficult to read when immediately followed by the punctuation for a type or value parameter list. Adding whitespace separates the two more clearly.
