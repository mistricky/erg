# ランク2型

Ergでは`id|T|(x: T) -> T = x`などのように色々な型を受け取れる関数、すなわち多相関数を定義できる。
では、多相関数を受け取れる関数は定義できるだろうか？
例えば、このような関数である(この定義は誤りを含むことに注意してほしい)。

```erg
# pair_map(i -> i * 2, (1, "a")) == (2, "aa")になってほしい
pair_map|T, L, R|(f: T -> T, (l: L, r: R)) -> (L, R) = (f(l), f(r))
```

`1`と`"a"`の型が違うので、無名関数は一度単相化して終わりではないことに注意してほしい。2回単相化する必要がある。
しかしはErgは型変数を一度しか単相化しない。すなわち、`T -> T`は`f(tup.0)`の時点で`Int -> Int`に決定されてしまう。

```erg
# pair_map: |L, R: Type; F <: L -> L; F <: R -> R|(F, (L, R)) -> (L, R)
pair_map|L, R|(f, (l: L, r: R)): (L, R) = (f(l), f(r))
```

何も指定しなかった場合はこうなる。

```erg
# pair_map: |F, T, U, V, W: Type; F <: T -> V; F <: U -> W| (F, (T, U)) -> (V, W)
pair_map(f, (l, r)) = (f(l), f(r))
```