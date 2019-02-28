# アセンブリ言語(Linux)

`AT&T構文`と`intel構文`がある（個人的に好きなのは`intel構文`）。

ファイルの拡張子を`.s`にして`gcc hoge.s`とすると実行ファイルにしてくれる

intel構文では`#`でコメントにできる。

`.`から始まる部分はアセンブラに何かしらの情報を伝える。

何も行わず、終了ステータスとして`0`を返す実行ファイルを作成するためのプログラムが以下のコード

```hoge.s
.intel_syntax noprefix # intel構文を使うことをアセンブラに教える
.global main # `main`を外部から見えるようにする。JavaScriptで言う`export`してるみたいな状態という認識

main: # `main`ラベル
  mov rax, 0 # `rax`レジスタに`0`を代入 ここを帰ると終了ステータスが変わる
  ret # 関数終了
```

## `rax`レジスタについて

関数の返り値を保存するのに使われる。

## 関数呼び出し

「引数はスタックに渡して〜」みたいなのを聞いたことあったけど、`x86_64`だと第6引数まで直接レジスタに渡すらしい。

|引数|レジスタ|
|:-:|:-:|
|第1引数|rdi|
|第2引数|rsi|
|第3引数|rdx|
|第4引数|rcx|
|第5引数|r8|
|第6引数|r9|

謎。なお、Windows向けのコードだとまた別のレジスタを使うらしい。謎。

また、第7引数以降はスタックに積むらしい。

また、呼び出し前に`rsp`を16バイトアライメントする必要がある…という記述を読んだが正確には理解できていない。

## `Hello, World!`

```func.s
.intel_syntax noprefix

# 使用する文字列定数を宣言する
.LC0:
  .string "Hello, World! %d\n"
.LC1:
  .string "a:%d b:%d\n"

.global main

# int func_add(int a, int b)
func_add:
  # 関数プロローグ
  push rbp
  mov rbp, rsp
  sub rsp, 16 # ここの数字は使用するローカル変数の数による

  # 引数をローカル変数に保存する
  # 「rbpレジスタが指すメモリアドレス - 8」番地に第1引数をコピーしている
  # ローカル変数はrbpレジスタからの相対位置で表現する
  mov [rbp - 8], rdi
  mov [rbp], rsi

  # leaと.LC1[rip]はよくわかっていない
  # printf("a:%d b:%d\n", a, b)
  lea rdi, .LC1[rip]
  mov rsi, [rbp - 8]
  mov rdx, [rbp]
  call printf@PLT

  # add
  mov eax, [rbp - 8]
  add eax, [rbp] # return value

  # 関数エピローグ
  mov rsp, rbp
  pop rbp
  ret

main:
  push rbp
  mov rbp, rsp

  # return func_add(10, 32)
  mov rdi, 10
  mov rsi, 32
  call func_add
  pop rbp
  ret
```

## 参考いろいろ

- https://www.sigbus.info/compilerbook/
- https://qiita.com/FAMASoon/items/a93c1361f80bb28f895c
