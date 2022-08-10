# Never

全ての型の下位型である。全てのメソッドを持っており、当然`.new`も持っているため`Class`である。しかしインスタンスは持たず、生成されそうになった瞬間にErgは停止する。
`Panic`という同じくインスタンスを持たない型が存在するが、正常に終了する際や意図的な無限ループの際は`Never`、異常終了する際には`Panic`を使う。

```erg
# Never <: Panic
f(): Panic = exit 0 # OK
g(): Never = panic() # TypeError
```

`Never`/`Panic`のOr型、例えば`T or Never`は`T`に変換することができる。これは、`Never`は意味論上起こり得ない(起こった場合プログラムは即時停止する)選択肢であるためである。
しかし、関数の戻り値型などで使用する場合、プログラムの終了が起こりうることを示すため`or Never`を省略することはできない。