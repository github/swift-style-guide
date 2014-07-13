##### Prefer implicit getters on computed properties

When possible, omit the `get` keyword on computed properties that cannot be written to.

So, do this:

```swift
var myGreatProperty: Int {
	return 4
}
```

… not this:

```swift
var myGreatProperty: Int {
	get {
		return 4
	}
}
```

_Rationale:_ The intent and meaning of the first version is clear, and results in less code.
