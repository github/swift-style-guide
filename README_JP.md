Swift コーディング規約

このドキュメントは以下に挙げるゴールを達成するために必要なパターンから構成されています。（順番が大まかな優先順位となっています）

 1. 精密さ・厳格さの向上, プログラマの思い込みによるエラーの減少
 1. 議論の明瞭性の向上
 1. 冗長性の減少
 1. 美学についての議論の削減

もしおすすめのパターンがあれば[ガイドライン](CONTRIBUTING.md)を読み、プルリクエストを送ってください。:zap:

また、日本語版のこのドキュメントでは Swift 言語仕様翻訳において[詳解Swift](http://www.amazon.co.jp/dp/4797380497)を参考としています。

----

#### 空白

 * タブを使う、スペースではなく
 * ファイル終端は改行で
 * 空白改行をロジック毎にコードを分けるときは使用する
 * 行の末端に空白は残さない
   * 空白行のインデント調整は除く


#### 可能な限り`let`宣言を`var`宣言より優先する

`var foo = …`より`let foo = …`を可能な限り（どちらか迷った時にも）使いましょう。本当に使わないといけない時にだけ`var`を使うようにしましょう。（具体的にはあなたがその値が変わり得ることを*知っている*ときや、`weak`プロパティ修飾子を使っている時などです）

_理由:_ 二つのキーワードの意図と意味が明瞭だからです。*デフォルトでlet*はより安全でより明確なコードを導き出します。

`let`宣言は、その値が続くことになっており、決して変わらないという*プログラマへの明確なサインとなります。なので、連続的なコードではそれが読む上で強力な手助けとなります。

[?]コードの理由付けがより簡単になります。値が決して変わらないかどうか悩んでいる時に使った`var`に対して、あなたはマニュアルでチェックしないといけません。

[?]よって、いかなる場合でも`var`宣言が使われているのを発見した時はその値が変わると仮定し、なぜなのかを考えながらコードを読むことができます。

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

トップレベルの関数、型、変数は常に明示的なアクセス制御があるべきでhそう。

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
