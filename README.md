# JavaScript勉強会＃flowtype

flowtypeは、Facebook製の*JavaScriptに対して型チェックをかける*ためのツールです。
Reactと相性が良いと言われていますが、フレームワークに縛られずに、柔軟さと堅牢さを保ったコーディングが実現できます。

## Setup

```
$ git clone https://github.com/KeitaMoromizato/js-study-flow
$ cd js-study-flow
$ npm i
```

## Code
### 01_success.js

flowtypeでチェックをするコードには、先頭に*/\* @flow \*/*とコメントを入れます。

```js
/* @flow */
const double = (n) => n * 2;

console.log(double(1));
```

簡単なコードであれば、何も書かずとも勝手に型推論してくれます。
上記の例だと、戻り値がnumber型であることだけでなく、仮引数nもnumber型だと推論されます(乗算ができる型である)。

## 02_fail.js

そして、型推論された結果、不整合が発生している場合は、flowtypeはエラーを起こします。
下記の例では、`dobule`関数はnumber型を期待しますが、引数としてstringを渡しているため、エラーが発生します。

```js
/* @flow */
const double = (n) => n * 2;

console.log(double("Hello"));
```

エラー内容

```
src/02_fail.js:4
  4: console.log(double("Hello"));
                 ^^^^^^^^^^^^^^^ function call
  2: const double = (n) => n * 2;
                           ^ string. This type is incompatible with
  2: const double = (n) => n * 2;
                           ^^^^^ number
```

## 03_typed.js

推論するだけでなく、明記することもできます。(同じ例なので意味無いですが...)

```js
/* @flow */
const double = (n: number) => n * 2;

console.log(double(5));
```

もちろんこのままだとJavaScriptのコードとして評価できないので、ビルドして型情報を除く必要があります(下記buildの項参照)。

## Test

flowtypeの型チェックを実施します

```js
$ npm run flow
```

このサンプルでは、`02_fail.js`が型チェックに失敗するようになっています。

自分で環境構築する場合は、`flow-bin`モジュールを使います。

```js
$ npm i flow-bin -D
```

`package.json`

```json
  "scripts": {
    "test": "flow"
  }
```

`.flowconfig`ファイルをプロジェクトルートに配置し、flowtypeの設定を記述します。デフォルトではnode_modules以下も見に行ってしまうので、`ignore`に指定します。

```
[ignore]
.*/node_modules/.*

[include]

[libs]

[options]
```

## build

型推論のみのファイルは問題ありませんが、コード中に型を書いているコードはそのままでは動きません。
(babelを使っている場合は)`babel-plugin-transform-flow-strip-types`というプラグインを組み込んでビルドすることで、コード中の型情報を削除できます。

```
$ npm i babel-plugin-transform-flow-strip-types -D
```

`.babelrc`

```json
{
  "presets": ["es2015"],
  "plugins": ["babel-plugin-transform-flow-strip-types"]
}
```