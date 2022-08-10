# Declaration(宣言)

宣言は、使用する変数の型を指定する構文です。
宣言はコード中のどこでも可能ですが、宣言しただけでその変数を参照することはできません。必ず初期化する必要があります。
代入後の宣言では、代入されたオブジェクトと型が適合するかをチェック可能です。

```erg
i: Int
# i: Int = 2のように代入と同時に宣言可能
i = 2
i: Num
i: Nat
i: -2..2
i: {2}
```

代入後の宣言は`assert`による型チェックと似ていますが、コンパイル時にチェックされるという特徴があります。
実行時の`assert`による型チェックは「〇〇型かもしれない」で検査が可能ですが、コンパイル時の`:`による型チェックは厳密です。
「〇〇型である」ことが確定していなくては検査を通らず、エラーとなります。

```erg
i = (-1..10).sample!()
assert i in Nat # これは通るかもしれない
i: Int # これは通る
i: Nat # これは通らない(-1はNatの要素ではないから)
```

関数は以下の4種類の方法で宣言が可能です。どれも戻り値型の省略はできません。

```erg
f(x: Int, y: Int): Int
f(Int, Int): Int
f: (x: Int, y: Int) -> Int
f: (Int, Int) -> Int
```

引数名を明示して宣言した場合、定義時に名前が違うと型エラーとなります。引数名の任意性を与えたい場合は2, 4番目の方法で宣言すると良いでしょう。その場合、型検査で見られるのはメソッド名とその型のみです。

```erg
T = Trait {
    .f(x: Int, y: Int): Int
}

C = Class(U, Impl: T)
C.f(a: Int, b: Int): Int = ... # TypeError: `.f` must be type of `(x: Int, y: Int) -> Int`, not `(a: Int, b: Int) -> Int`
```

<p align='center'>
    <a href='./02_name.md'>Previous</a> | <a href='./04_function.md'>Next</a>
</p>