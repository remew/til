# `goroutine`とは

`軽量スレッド`と呼ばれる並行処理の仕組み。

スレッドっぽいけど生成コストやメモリの使用量などが少ない(らしい)。

Go言語が並行処理に強いと言われる理由の一つがこの`goroutine`。

## `goroutine`の作り方

`goroutine`の作り方は非常に簡単で、関数呼び出しの際に`go`というキーワードを付与することで、「`goroutine`の生成」「生成した`goroutine`上で関数実行」を行ってくれる。

```go
func f(n int) {
  for i := 0; i < n; i++ {
    fmt.Println(i)
    time.Sleep(1 * time.Second) // 1秒待つ
  }
}

func main() {
  go f(10); // goroutineを生成してfを実行
  fmt.Println("go") // 数字が表示されるより前にgoが表示される
  for {}
}
```

非常に手軽に別のスレッド(のようなもの)を作ることができるのもGo言語の特徴。

## JavaScriptとの比較

`Node.js(JavaScript)`だと、1つのスレッドでイベントループを回して非同期処理を行うためスレッド生成コストがかからないが、どこかでイベントループがブロックされると処理全体が止まってしまったり、マルチコア性能を活かせなかったりするデメリットがある。<br>
`goroutine`であれば、スレッドの生成コストを抑えつつもマルチコアを活かすことができる。

## `goroutine`と`channel`

Go言語では、`channel`と呼ばれる機能を用いて`goroutine`間でデータをやり取りすることができる。

`channel`にはデータを送ったり、データを取り出したりすることができる。

`channel`の定義は`var (変数名) chan (やり取りするデータ型)`として行う。例`var c chan int`。

`channel`の生成は`make(chan (やり取りするデータ型))`のように行う。

`channel`からデータを取り出すときは`v := <- c`、送るときは`c <- v`のような記法を用いる。

```go
func f1(c chan int) {
  for {
    v := <- c
    fmt.Println(c)
  }
}
func f2(c chan int) {
  count := 0
  for {
    time.Sleep(100 * time.Millisecond)
    c <- count
    count++
  }
}
```

TODO: `select`について
