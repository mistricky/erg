# 型拡大(Type Widening)

例えば以下のような多相関数を定義する。

```erg
ids|T|(x: T, y: T) = x, y
```

同じクラスのインスタンスペアを代入する分には何の問題もない。
包含関係にある別のクラスのインスタンスペアを代入すると、大きい方にアップキャストされて同じ型になる。
また、包含関係にない別のクラスを代入するとエラーになるのも容易に理解できる。

```erg
assert ids(1, 2) == (1, 2)
assert ids(1, 2.0) == (1.0, 2.0)
ids(1, "a") # TypeError
```

さて、では別の構造型を持つ型の場合はどうなるのだろうか。

```erg
i: Int or Str
j: Int or NoneType
ids(i, j) # ?
```

これの説明を行う前に、Ergの型システムが実は(実行時の)クラスを見ていないという事実に注目しなくてはならない。

```erg
1: {__valueclass_tag__ = Phantom Int}
2: {__valueclass_tag__ = Phantom Int}
2.0: {__valueclass_tag__ = Phantom Ratio}
"a": {__valueclass_tag__ = Phantom Str}
ids(1, 2): {__valueclass_tag__ = Phantom Int} and {__valueclass_tag__ = Phantom Int} == {__valueclass_tag__ = Phantom Int}
ids(1, 2.0): {__valueclass_tag__ = Phantom Int} and {__valueclass_tag__ = Phantom Ratio} == {__valueclass_tag__ = Phantom Ratio} # Int < Ratio
ids(1, "a"): {__valueclass_tag__ = Phantom Int} and {__valueclass_tag__ = Phantom Str} == Never # TypeError
```

クラスを見ていないというのは、正確には見られない場合があるからで、これはErgにおいてオブジェクトのクラスは実行時情報に属するためである。
例えば、`Int or Str`型オブジェクトのクラスは`Int`または`Str`であるが、これがどちらなのかは実行してはじめてわかることである。
もちろん`Int`型のオブジェクトのクラスは`Int`で確定であるが、この場合も型システムから見えるのは`Int`の構造型`{__valueclass_tag__ = Int}`である。

さて、別の構造型の例に戻ろう。結論から言うと上のコードは型があっていないとしてTypeErrorになる。
しかし型注釈で型拡大を行えばコンパイルが通る。

```erg
i: Int or Str
j: Int or NoneType
ids(i, j) # TypeError: types of i and j not matched
# hint: try type widening (e.g. ids<Int or Str or NoneType>)
ids<Int or Str or NoneType>(i, j) # OK
```

`A and B`は以下の可能性がある。

* `A and B == A`: `A < B`または`A == B`のとき。
* `A and B == B`: `A > B`または`A == B`のとき。
* `A and B == {}`: `A != B`のとき。

`A or B`は以下の可能性がある。

* `A or B == A`: `A > B`または`A == B`のとき。
* `A or B == B`: `A < B`または`A == B`のとき。
* `A or B`は簡約不能(独立した型): `A != B`のとき。

## サブルーチン定義での型拡大

Ergでは、戻り値型が一致しない場合デフォルトでエラーとなる。

```erg
parse_to_int s: Str =
    if not s.is_numeric():
        do parse_to_int::return error("not numeric")
    ... # return Int object
# TypeError: mismatch types of return values
#     3 | do parse_to_int::return error("not numeric")
#                                 └─ Error
#     4 | ...
#         └ Int
```

これを解決するためには、戻り値型を明示的にOr型と指定する必要がある。

```erg
parse_to_int(s: Str): Int or Error =
    if not s.is_numeric():
        do parse_to_int::return error("not numeric")
    ... # return Int object
```

これは、サブルーチンの戻り値型に意図せず別の型を混入させないようにという設計である。
ただし、戻り値型の選択肢が`Int`か`Nat`など包含関係がある型であった場合、大きい方に揃えられる。
