# Go言語

## 関数

Go言語での関数定義の方法

```go
func f() {
  fmt.Println("Called `f`")
}
```

### 引数の書き方

引数の型は後置で、同じ型が続く場合は省略できる

```go
func f1(a int) int {
  return a * a
}

func f2(a, b int) int {
  return a * b
}

func f3(a float64, b, c int, d string) {
  fmt.Println(a, b, c, d)
}
```

### 返り値の書き方

返り値の型も後置で、複数の値を返すときの型はカッコで囲んでカンマ区切りにする(return文はカッコで囲まなくても良い)

```go
func swap(a, b int) (int, int) {
  return b, a
}
```

## 変数

`var`で宣言する。型は後置。

```go
func main() {
  var a int
  fmt.Println(a) // 0
}
```

`:=`は変数宣言と代入を同時に行うときに使える構文で、`var`が不要になる

```go
func f() int {
  a := 1
  return a
}
```

定数は`const`を付けて宣言する。`:=`構文は使えない

```go
func f() string {
  const s = "Hello"
  return s
}
```

## 基本型

詳細は雰囲気で(TODO: ちゃんと調べる)

カッコ内は初期値

- `bool`(false)
- `string`("")
- `int`, `int8`, `int16`, `int32`, `int64`, `uint`, `uint8`, `uint16`, `uint32`, `uint64`, `uintptr`(0)
- `byte`(`uint8`のエイリアス)(0)
- `rune`(`int32`のエイリアスで、ユニコードのコードポイントを表現できる)(0)
- `float32`, `float64`(0)
- `complex64`, `complex128`(0+0i)

`int`, `uint`, `uintptr`のサイズは32bitシステムか64bitシステムかで変わる。


