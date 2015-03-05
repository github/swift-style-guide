Swift コーディング規約

このドキュメントは、以下に挙げる目標を達成できる方法を促進するための試みとして作成されたものです。（大まかな優先度順となっています）

 1. より厳密で、プログラマが誤解する可能性が少ないこと
 1. 意図が明確であること
 1. 冗長さが排除されていること
 1. 美学についての議論が少ないこと

もし提案があれば、[ガイドライン](CONTRIBUTING.md)を読み、プルリクエストを送ってください。:zap:

また、日本語版のこのドキュメントでは Swift 言語仕様翻訳において[詳解Swift](http://www.amazon.co.jp/dp/4797380497)を参考としています。

----

#### 空白

 * スペースではなく、タブを使う
 * ファイル終端は改行する
 * コードをロジック毎に分割するために、空白行を惜しみなく使う
 * 行末に空白を残さない
   * 空白行でのインデント調整もしない


#### 可能な限り`var`宣言よりも`let`宣言を使う

可能な限り（どちらか迷った時にも）`var foo = …`より`let foo = …`を使いましょう。`var`は本当に使わないといけない時にだけ使うようにしましょう。（具体的には、あなたがその値が変わり得ることを*知っている*ときや、`weak`プロパティ修飾子を使っている時などです）

_理由:_ 二つのキーワードの意図と意味は明瞭ですが、*デフォルトでlet*を使うことは、より安全でより明確なコードになります。

`let`宣言は、その変数の値が変わらないとを想定されていて、かつ実際に変わらない
ことを保証すると同時に、それをプログラマに明確に伝えます。そのため、その後に続くコードにおいて、その変数の用途を推測しやすくなります。

コードを論理的に理解するのがより簡単になります。値が決して変わらないと考えているにもかかわらず`var`を使うと、本当に値が変わらないかどうかを手動でチェックしなければいけません。

この方法に従うと結果的に、`var`宣言が使われているのを発見した時は必ず、その値が変わり得ると推測したり、その理由を考えることができます。


#### オプショナル型の開示指定は避ける

`FooType?`もしくは`FooType!`型の変数`foo`がある場合、そこにある`foo!`を得ようとして`foo`に対して開示指定を行うのは可能な限り避けないといけません。

とにかく以下を参照してください:

```swift
if let foo = foo {
    // 開示された`foo`の値を使う
} else {
    // 必要な場合はここでオプショナルがnilの場合の処理を行う
}
```

他の方法として、Swift のオプショナルチェーンを以下のように使いたくなる場面もあると思います。

```swift
// `foo`がnilでない場合は関数を呼ぶ、`foo`がnilの場合は関数呼び出し自体を無視する
foo?.callSomethingIfFooIsNotNil()
```

_理由:_ 明示的な `if let`によるオプショナル束縛構文は安全なコードを生み出すからです。 開示指定はランタイムでのクラッシュを生み出してしまう可能性があります。

#### 暗黙的開示オプショナル型の使用を避ける

もし`foo`がnilになる場合がある場合、可能な限り`let foo: FooType?`を使うべきです、`let foo: FooType!`ではなく。（通常は`?`と覚えておく、`!`の代わりに`?`を使ってしまう場合もあるかもしれないが）

_理由:_ 明示的なオプショナル型は安全なコードを生み出すからです。暗黙的開示オプショナル型はランタイム時のクラッシュを生み出す可能性を持っています。

#### 読み取り専用のプロパティと添字付けでは暗黙的なgetterを優先する

可能な限り、読み取り専用のプロパティと添字付けでは`get`キーワードを省きましょう。

このように書きます。

```swift
var myGreatProperty: Int {
	return 4
}

subscript(index: Int) -> T {
    return objects[index]
}
```

… こうではなく

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

_理由:_ 一つ目の方が意図と意味が明確だからです。またコードも少なく済みます。

#### 常にトップレベルの定義にはアクセス制御を明示化する

トップレベルの関数、型、変数は常に明示的なアクセス制御があるべきでしょう。

```swift
public var whoopsGlobalState: Int
internal struct TheFez {}
private func doTheThings(things: [Thing]) {}
```

しかし、トップレベルの定義の中のものについてのアクセス制御は問題なければ暗黙的にできます。

```swift
internal struct TheFez {
	var owner: Person = Joshaber()
}
```

_理由:_ トップレベルの定義で明示的に`internal`と書くこと自体が有効になることは滅多にないでしょう。そして明確にすることはアクセス制御に対して注意深い姿勢を持っていることを示してくれます。定義において、同じアクセス制御を繰り返すことはただ冗長なだけなので、デフォルト値が通常わかりやすいです。

#### 型の指定は常に識別子の後ろにコロンと繋げる

識別子に型を指定する時、常に識別子の後ろにコロンを置き空白を一つあけて型名を書きましょう。

```swift
class SmallBatchSustainableFairtrade: Coffee { ... }

let timeToCoffee: NSTimeInterval = 2

func makeCoffee(type: CoffeeType) -> Coffee { ... }
```

_理由:_ 型指定は_識別子_に対してなんらかの意味づけをしてくれるからです。なので型指定は識別子の近くに定義するべきです。

同じように、辞書の方定義をするときには常にキーの型の後にコロンをおいて空白の後に値の方を指定します。

```swift
let capitals: [Country: City] = [ Sweden: Stockholm ]
```

#### 明示的な`self`参照は必要な時だけ

`self`の持つプロパティやメソッドへアクセスする時、デフォルトでは`self`への参照は省きましょう。

```swift
private class History {
	var events: [Event]

	func rewrite() {
		events = []
	}
}
```

明示的に`self`を入れるのは言語によって必要と判断された場合だけにしましょう-たとえばクロージャ内、もしくはパラメータ名の衝突などです。

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

_理由:_ これによってクロージャにおける`self`のキャプチャリングが目立つようになります。そしてその他の場所における冗長さが無くなります。

#### クラスより構造体を優先する

クラスでしか提供できない機能（同一性やデイニシャライザなど）を必要としない限り、クラスではなく構造体で実装しましょう。

継承は通常、クラスを使う主な良い理由には_なりません_、なぜなら多様性はプロトコルを用いることで提供できるからです。そしてその実装はコンポジションによって再利用も可能です。

例として以下のクラス構造をあげます。

```swift
class Vehicle {
    let numberOfWheels: Int

    init(numberOfWheels: Int) {
        self.numberOfWheels = numberOfWheels
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

上記は以下のようにプロトコルと構造体で書き換えられます。

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

_理由:_ 値型はシンプルで説明しやすく、`let`の挙動通りに動作してくれるからです。

#### デフォルトでクラスを`final`にする

クラス定義は`final`ではじめるべきです、そして明確な継承の必要性が発生してサブクラス化を許容する場合だけ変更するのです。[?]上記の例では、それぞれのクラスにも`final`を同じように適用します。

_理由:_ 通常コンポジションは継承を実現するのに望ましいです、[?]クラス継承を選択する場合、それよりも多くの労力が必要になるでしょう。

#### 可能な限り型パラメータは省く

[?]型パラメータ化されたメソッドはレシーバが一意の場合は引数の型パラメータを省くことができます。たとえば

```swift
struct Composite<T> {
	…
	func compose(other: Composite<T>) -> Composite<T> {
		return Composite<T>(self, other)
	}
}
```

上記は以下のように書くことができます。

```swift
struct Composite<T> {
	…
	func compose(other: Composite) -> Composite {
		return Composite(self, other)
	}
}
```

_理由:_ 冗長な型パラメータを省くことで意図が明確になりからです。そして返り値の型が違う型を取る場合をより明確に示すことができます。

#### オペレータ定義では空白を使う

オペレータを定義するときは空白を使いましょう。
こうではなく

```swift
func <|(lhs: Int, rhs: Int) -> Int
func <|<<A>(lhs: A, rhs: A) -> A
```

このように書きましょう

```swift
func <| (lhs: Int, rhs: Int) -> Int
func <|< <A>(lhs: A, rhs: A) -> A
```

_理由:_ オペレータは読むことのできない文字でできており、それらがパラメータのリストとくっついているとすぐに理解できないからです。明確に空白をつけましょう。

#### 翻訳

* [中文版](https://github.com/Artwalk/swift-style-guide/blob/master/README_CN.md)
