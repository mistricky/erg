# 型に関する基本的な文法

## 型指定

Ergでは以下のように`:`の後に変数の型を指定します。代入と同時に行うこともできます。

```erg
i: Int # これから使う変数iはInt型であると宣言する
i: Int = 1
j = 1 # type specification can be omitted
```

単純な変数代入の場合、ほとんどの型指定は省略可能です。
型指定は単純な変数よりもサブルーチンや型の定義時に役立ちます。

```erg
# 引数の型指定
f x, y: Array Int = ...
T X, Y: Array Int = ...
```

上の場合、`x, y`は共に`Array Int`であることに注意して下さい。

```erg
# 大文字変数の値は定数式でなくてはならない
f X: Int = X
```

あるいは、型引数の情報が完全にいらない場合は`_`で省略することもできます。

```erg
g v: [T; _] = ...
```

ただし、型指定の箇所で`_`を指定するとそれは`Object`を意味することに注意して下さい。

```erg
f x: _, y: Int = x + y # TypeError: + is not implemented between Object and Int
```

## 部分型指定

Ergでは`:`(型宣言演算子)による型と式の関係指定の他に、`<:`(部分型宣言演算子)で型同士の関係を指定することもできます。
`<:`の左辺はクラスしか指定できません。構造型同士の比較は`Subtypeof`などを使用して下さい。

これも単純な変数の指定より、サブルーチンや型の定義時に使うことが多いです。

```erg
# 引数の部分型指定
f X <: T = ...

# 要求属性の部分型指定(.Iterator属性はIterator型のサブタイプであることを要求する)
Iterable T = Trait {
    .Iterator = {Iterator} # == {I | I <: Iterator}
    .iter = Self.() -> Self.Iterator T
    ...
}
```

また、クラス定義時に部分型指定を行うと、クラスが指定した型のサブタイプか静的に検査することができます。

```erg
# クラスCはShowのサブタイプ
C = Class Object, Impl=Show
C.show self = ... # Showの要求属性
```

特定の場合だけ部分型指定することもできます。

```erg
K T: Eq
K Int <: Show and Eq
K T = Class Object
K(T).
    `==` self, other = ...
K(Int).
    show self = ...
```

構造型を実装する際は、部分型指定を行うことを推奨します。
構造的部分型付けの特性から、要求属性の実装をする際にタイポや型指定の間違いがあってもエラーが出ないためです。

```erg
C = Class Object
C.shoe self = ... # TypoのせいでShowが実装できていない(単なる固有のメソッドとみなされる)
```

## 属性定義

トレイトやクラスには、モジュール内でのみ属性を定義することができます。

```erg
C = Class()
C.pub_attr = "this is public"
C::private_attr = "this is private"

c = C.new()
assert c.pub_attr == "this is public"
```

`C.`か`C::`のあとに改行してインデント以下にまとめて定義する文法を一括定義(batch definition)といいます。

```erg
C = Class()
C.pub1 = ...
C.pub2 = ...
C::priv1 = ...
C::priv2 = ...
# is equivalent to
C = Class()
C.
    pub1 = ...
    pub2 = ...
C::
    priv1 = ...
    priv2 = ...
```

## エイリアシング

型には別名(エイリアス)を付けることができます。これにより、レコード型など長い型を短く表現できます。

```erg
Id = Int
Point3D = {x = Int; y = Int; z = Int}
IorS = Int or Str
Vector = Array Int
```

またエラー表示の際にも、コンパイラは複合型(上の例の場合、1番目以外の右辺型)にエイリアスが定義されている場合なるべくそれを使用するようになります。

ただし同じ型のエイリアスは1つのモジュールにつき1つまでで、複数のエイリアスがある場合warningが出ます。
これは、違う目的の型は別々の型として新しく定義するべき、ということです。
また、すでにエイリアスのある型に重ねてエイリアスを付けることを防ぐ目的もあります。

```erg
Id = Int
UserId = Int # TypeWarning: duplicate aliases: Id and UserId

Ids = Array Id
Ints = Array Int # TypeWarning: duplicate aliases: Isd and Ints

IorS = Int or Str
IorSorB = IorS or Bool
IorSorB_ = Int or Str or Bool # TypeWarning: duplicate aliases: IorSorB and IorSorB_

Point2D = {x = Int; y = Int}
Point3D = {...Point2D; z = Int}
Point = {x = Int; y = Int; z = Int} # TypeWarning: duplicate aliases: Point3D and Point
```

<p align='center'>
    <a href='./01_type_system.md'>Previous</a> | <a href='./03_trait.md'>Next</a>
</p>