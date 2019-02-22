# `TypeScript`で`.png`ファイルを読み込む

## TL;DR

1. `file-loader`や`url-loader`を使う
1. `resource.d.ts`を定義する

## `file-loader`について

`webpack`のローダーとして`file-loader`を使うと、画像ファイルやフォントファイルなどをURL文字列として`import`することができる。

```js
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {},
          },
        ],
      },
    ],
  },
};
```

```js
// Icon.js
import icon from '../asset/icon.png';

export default function Icon() {
  return <img src={icon} alt='my icon' />
}
```

これをwebpackでビルドすると、`icon.png`が出力ディレクトリに`1q2w3e4r.png`のようなファイル名でコピーされ、`Icons.js`の`icon`にはファイル名が渡される。

## `url-loader`

任意のファイルをURL文字列として読み込めるのは`file-loader`と同じ。ただし読み込むURL文字列は`base64 URL`になる。

ファイルサイズが小さい画像を大量にリクエストするより、多少バンドル結果のJSファイルのサイズを増やしてでもリクエストのオーバーヘッドを減らすのが目的だと思われる。

`webpack.config.js`の例は省略

## `resource.d.ts`について

`tsconfig.js`と同じディレクトリに`typings`というディレクトリを作成する（名前は何でも良い）。

その中に`resource.d.ts`というファイルを作成する（名前は何でも良い）。

```ts
declare module "*.png" {
  const content: string;
  export default content;
}
```

これで、 `TypeScript`と`webpack`を組み合わせたプロジェクトで`file-loader`や`url-loader`を使えるようになるはず。
