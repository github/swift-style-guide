Swift 编码规范  

本文尝试做到以下几点 （大概的先后顺序）：

1. 增进精确，减少程序员犯错的可能
1. 明确意图
1. 减少冗余
1. 少量关于美的讨论

如果你有什么建议，请看我们的 [贡献导引](CONTRIBUTING.md)，然后开个 `pull request`.  :zap:

----

#### 留空白

 * 用 Tabs，而非 空格
 * 文件结束时留一空行
 * 用足够的空行把代码分割成合理的块
 * 不要在一行结尾留下空白
   * 千万别在空行留下缩进

#### 对于只读属性的 `properties` 和 `subscripts`，选用隐式的 getters 方法

如果可以，省略只读属性的 `properties` 和 `subscripts` 的 `get` 关键字

所以，酱紫敲代码：

```swift
var myGreatProperty: Int {
	return 4
}

subscript(index: Int) -> T {
    return objects[index]
}
```

… 而不是酱紫：

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

_理由:_ 第一个版本的代码意图已经很清楚了，并且用了更少的代码

#### 对于顶级定义，永远明确的列出权限控制

顶级函数，类型和变量，永远应该有着详尽的权限控制说明符

```swift
public var whoopsGlobalState: Int
internal struct TheFez {}
private func doTheThings(things: [Thing]) {}
```

当然，这样也是恰当的，因为用了隐式权限控制

```swift
internal struct TheFez {
	var owner: Person = Joshaber()
}
```

_理由:_ 顶级定义指定为 `internal`很少有恰当的，要明确的确保经过了仔细的判断。有了一个定义，重用同样的权限控制说明符就显得重复，所以默认的通常是合理的。

#### 当指定一个类型时，把 冒号和标识符 连在一起

当指定标示符的类型时，冒号要紧跟着标示符，然后空一格再写类型

```swift
class SmallBatchSustainableFairtrade: Coffee { ... }

let timeToCoffee: NSTimeInterval = 2

func makeCoffee(type: CoffeeType) -> Coffee { ... }
```

_理由:_ 类型区分号是对于 _identifier_ 来说的，所以要跟它连在一起。

#### 需要时才写上 `self`

当调用 `self` 的 `properties` 或 `methods` 时，`self` 用默认的隐式引用：

```swift
private class History {
	var events: [Event]

	func rewrite() {
		events = []
	}
}
```

必要的时候再加上`self`, 比如在闭包里，或者 参数名冲突了：

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

_原因:_ 在闭包里用`self`更加凸显它的语义，并且避免了别处的冗长

#### 相对于 `classes` 先选 `structs`

除非你需要 `class` 才能提供的功能（比如 `identity` 或 `deinitializers`），不然就用 `struct`

要注意到继承通常**不**是用 类 的好理由，因为 多态 可以通过 协议 实现，重用 可以通过 组合 实现。

比如，这个类的分级

```swift
class Vehicle {
    let numberOfWheels: Int

    init(numberOfWheels: Int) {
        self.numberOfWheels = numberOfWheels;
    }

    func maximumTotalTirePressure(pressurePerWheel: Float) -> Float {
        return pressurePerWheel * numberOfWheels
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

可以重构成酱紫：

```swift
protocol Vehicle {
    var numberOfWheels: Int { get }
}

func maximumTotalTirePressure(vehicle: Vehicle, pressurePerWheel: Float) -> Float {
    return pressurePerWheel * vehicle.numberOfWheels
}

struct Bicycle: Vehicle {
    let numberOfWheels = 2
}

struct Car: Vehicle {
    let numberOfWheels = 4
}
```

_理由:_ 值的类型更简单，容易辨别，并且通过`let`关键字可猜测行为。

#### 默认 `classes` 为 `final`

`Classes` 应该作为基类，只能被子类已识别正当的继承（and only be changed to allow subclassing if a valid need for inheritance has been identified.）。即使这种例子，根据同样的规则，类中的定义也要尽可能的用 `final` 标注上

_理由:_ 组合通常比继承更合适，而且不用 继承意味着考虑的更多（and opting in to inheritance hopefully means that more thought will be put into the decision.）。

#### 能不写类型参数的就别写了

参数化类型的方法可以省略接收者的类型参数，当他们对接收者来说一样时。比如：

```swift
struct Composite<T> {
	…
	func compose(other: Composite<T>) -> Composite<T> {
		return Composite<T>(self, other)
	}
}
```

可以这样写：

```swift
struct Composite<T> {
	…
	func compose(other: Composite) -> Composite {
		return Composite(self, other)
	}
}
```

_理由:_ 省略多余的类型参数让意图更清晰，并且通过对比，让返回值为不同的类型参数的情况也清楚了很多。

---

PS:   
如果看不懂，一定是我翻译的不好 233333  
[原版](https://github.com/github/swift-style-guide) 戳这里  
哪里不对或者不准确的，若能指出   
感激不尽~~~  