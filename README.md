##### Prefer implicit getters on computed properties

Prefer:

```swift
var myGreatProperty: Int {
	return 4
}
```

over:

```swift
var myGreatProperty: Int {
	get {
		return 4
	}
}
```

_Rationale:_ The intent and meaning of the first version is clear, and results in less code.
