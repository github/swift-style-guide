## Intro

A guide to our Swift style and conventions.

This is an attempt to encourage patterns that accomplish the following goals (in
rough priority order):

 1. Increased rigor, and decreased likelihood of programmer error
 1. Increased clarity of intent
 1. Reduced verbosity
 1. Fewer debates about aesthetics

If you have suggestions, please see our [contribution guidelines](CONTRIBUTING.md),
then open a pull request. :zap:

----

## Style Guide Outline

This is not an outline of the style guide. _The outline is the style guide._ The outline has enough information to be understood, and used as a quick reference. The [Style Guide Rationale & Discussion](#style-guide-rationale-discussion) below supports the outline and are linked in outline numbers (minor bullet points not linked). Outline numerals may change and are not intended for citation at this time. 

*Clarity* means anything that will not affect the logic of the code. *Usage* refers to code usage choices.

* [[I.]](#clarity) Clarity
	* [[I.A.]](#horizontal-whitespace) Horizontal Whitespace 
		 * [I.A.1.] Tabs or spaces? Tabs
		 * [I.A.2.] Don't leave trailing whitespace
		 * [[I.A.3.]](#when-specifying-a-type-always-associate-the-colon-with-the-identifier) When specifying a type, always associate the colon with the identifier [[more]]
		 * [[I.A.4.]](#use-whitespace-around-operator-definitions) Use whitespace around operator definitions

	* [[I.B.]](#vertical-whitespace) Vertical Whitespace
		* [[I.B.1.]](#end-files-with-a-newline) End files with a newline.
 		* [[I.B.2.]](#make-liberal-use-of-vertical-whitespace-to-divide-code-into-logical-chunks) Make liberal use of vertical whitespace to divide code into logical chunks.
 
	* [[I.C.]](#omissions) Omissions
		* [[II.C.1.]](#prefer-implicit-getters-on-read-only-properties-and-subscripts) Prefer implicit getters on read-only properties and subscripts
		* [[II.C.2.]](#always-specify-access-control-explicitly-for-top-level-definitions) Always specify access control explicitly for top-level definitions
		* [[II.C.3.]](#only-explicitly-refer-to-self-when-required) Only explicitly refer to `self` when required
		* [[II.C.4.]](#omit-type-parameters-where-possible) Omit type parameters where possible
		 
* [[II.]](#usage) Usage 

 	* [[II.A.]](#parameters-and-types) Types 
		* [[II.A.1.]](#prefer-let-bindings-over-var-bindings-wherever-possible) Prefer `let`-bindings over `var`-bindings wherever possible
		* [[II.A.3.]](#prefer-structs-over-classes) Prefer structs over classes
		* [[II.A.4.]](#make-classes-final-by-default) Make classes `final` by default
		
 	* [[II.B.]](#optionals) Optionals

		* [[II.B.1.]](#avoid-using-force-unwrapping-of-optionals) Avoid Using Force-Unwrapping of Optionals
		* [[II.B.2.]](#avoid-using-implicitly-unwrapped-optionals) Avoid Using Implicitly Unwrapped Optionals

	* [[II.C.]](#control-flow) Control Flow
		* [[II.C.1.]](#return-and-break-early) Return and break early

----

## Style Guide Rationale & Discussion

### Clarity  

#### Horizontal Whitespace

* Tabs or spaces? Tabs

* Don’t leave trailing whitespace.

	*Not even leading indentation on blank lines.
   
##### When specifying a type, always associate the colon with the identifier

When specifying the type of an identifier, always put the colon immediately
after the identifier, followed by a space and then the type name.

```swift
class SmallBatchSustainableFairtrade: Coffee { ... }

let timeToCoffee: NSTimeInterval = 2

func makeCoffee(type: CoffeeType) -> Coffee { ... }
```

_Rationale:_ The type specifier is saying something about the _identifier_ so
it should be positioned with it.

Also, when specifying the type of a dictionary, always put the colon immediately
after the key type, followed by a space and then the value type.

```swift
let capitals: [Country: City] = [ Sweden: Stockholm ]
```


##### Use whitespace around operator definitions

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


#### Vertical Whitespace

 * End files with a newline.
 * Make liberal use of vertical whitespace to divide code into logical chunks.

#### Omissions

##### Prefer implicit getters on read-only properties and subscripts

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

##### Always specify access control explicitly for top-level definitions

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

_Rationale:_ It's rarely appropriate for top-level definitions to be specifically `internal`, and being explicit ensures that careful thought goes into that decision. Within a definition, reusing the same access control specifier is just duplicative, and the default is usually reasonable.

##### Only explicitly refer to `self` when required

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


##### Omit type parameters where possible

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

### Usage

#### Parameters and Types

##### Prefer `let`-bindings over `var`-bindings wherever possible

Use `let foo = …` over `var foo = …` wherever possible (and when in doubt). Only use `var` if you absolutely have to (i.e. you *know* that the value might change, e.g. when using the `weak` storage modifier).

_Rationale:_ The intent and meaning of both keywords is clear, but *let-by-default* results in safer and clearer code.

A `let`-binding guarantees and *clearly signals to the programmer* that its value will never change. Subsequent code can thus make stronger assumptions about its usage.

It becomes easier to reason about code. Had you used `var` while still making the assumption that the value never changed, you would have to manually check that.

Accordingly, whenever you see a `var` identifier being used, assume that it will change and ask yourself why.

#### Prefer structs over classes

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

func maximumTotalTirePressure(vehicle: Vehicle, pressurePerWheel: Float) -> Float {
    return pressurePerWheel * Float(vehicle.numberOfWheels)
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



#### Optionals

##### Avoid Using Force-Unwrapping of Optionals

If you have an identifier `foo` of type `FooType?` or `FooType!`, don't force-unwrap it to get to the underlying value (`foo!`) if possible.

Instead, prefer this:

```swift
if let foo = foo {
    // Use unwrapped `foo` value in here
} else {
    // If appropriate, handle the case where the optional is nil
}
```

Alternatively, you might want to use Swift's Optional Chaining in some of these cases, such as:

```swift
// Call the function if `foo` is not nil. If `foo` is nil, ignore we ever tried to make the call
foo?.callSomethingIfFooIsNotNil()
```

_Rationale:_ Explicit `if let`-binding of optionals results in safer code. Force unwrapping is more prone to lead to runtime crashes.

##### Avoid Using Implicitly Unwrapped Optionals

Where possible, use `let foo: FooType?` instead of `let foo: FooType!` if `foo` may be nil (Note that in general, `?` can be used instead of `!`).

_Rationale:_ Explicit optionals result in safer code. Implicitly unwrapped optionals have the potential of crashing at runtime.


#### Control Flow

##### Return and break early

When you have to meet certain criteria to continue execution, try to exit early. So, instead of this:

```swift
if n.isNumber {
    // Use n here
} else {
    return
}
```

use this:
```swift
guard n.isNumber else {
    return
}
// Use n here
```

You can also do it with `if` statement, but using `guard` is preferred, because `guard` statement without `return`, `break` or `continue` produces a compile-time error, so exit is guaranteed.


#### Translations

* [中文版](https://github.com/Artwalk/swift-style-guide/blob/master/README_CN.md)
* [日本語版](https://github.com/jarinosuke/swift-style-guide/blob/master/README_JP.md)
* [한국어판](https://github.com/minsOne/swift-style-guide/blob/master/README_KR.md)
* [Versión en Español](https://github.com/antoniosejas/swift-style-guide/blob/spanish/README-ES.md)
* [Versão em Português do Brasil](https://github.com/fernandocastor/swift-style-guide/blob/master/README-PTBR.md)
