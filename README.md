# jQuery で忙しい人のための Riot.js を最短で使うための手引き

## 目次
- [はじめに](#intro)
- [jQuery、なんだかんだ楽チンですよね。](#jquery)
- [Riot.js はそんな風に思っているあなたのためのライブラリ。](#for-you)
- [jQuery 同様、`script` で読み込むだけ！](#just-script)
- [サンプル 1: とりあえず使ってみる](#sample-1)
- [サンプル 2: `.riot` の中に CSS を書く](#sample-2)
- [サンプル 3: `.riot` の中に JavaScript を書く](#sample-3)
- [サンプル 4: `if` ディレクティブを使って HTML を出し分けてみる](#sample-4)
- [サンプル 5: 三項演算子で `class` 属性値を出し分ける](#sample-5)

## <a id="intro">はじめに</a>

これはタイトルの通り、jQuery には慣れてるけど、最近の複雑なフロントエンド周りに苦手意識がある Web デザイナーのような方に向けて、Riot.js を使って Web の制作でラクをするための Tips をまとめたものです。

僕自身が jQuery には長年お世話になった（今もなっている）Web デザイナーの一人なので、そういうところから Riot.js の良いところなどを伝えられればよいなとおもってこのリポジトリを作成しました。

順次追記していくスタイルですが、

- 「こういうことできない？」
- 「ここがわからん」
- 「こういうこと書いたほうがいいんじゃない？」
- 「ここまちがってるよ」

などなど、書き途中でもフィードバックは issue のほうで順次募集しています。

よろしくおねがいします。

## <a id="jquery">jQuery、なんだかんだ楽チンですよね。</a>

jQuery は長らく Web フロントエンドの「魔法の杖」でしたが、数年前に SPA に強い次世代のライブラリやフレームワークが登場して以降は、jQuery はもう過去のモノ、みたいに言われることもあります。

たしかに、ある意味ではそうかもしれません。
jQuery の機能の一部は、現在では素の JavaScript でできるようになりましたし、最近のフレームワークに比べれば jQuery は複雑な UI を作り込むのにはあまり向いていません。

だけど、小規模な Web 制作や単発の機能の開発であれば、プラグインも豊富で、何より `script` で読み込むだけで簡単に導入できる jQuery が生きるシーンもあると思います。

## <a id="for-you">Riot.js はそんな風に思っているあなたのためのライブラリ。</a>

[Riot.js](https://riot.js.org/ja/) は、近年日本でもユーザーが急増している Vue.js などと同じく、HTML / CSS / JavaScript といった Web の見た目に関わる部分を効率的に管理・制作するための JavaScript ライブラリです。

[Riot.js — Simple and elegant component-based UI library · Riot.js](https://riot.js.org/ja/)

そこで、Riot.js をつかってコンポーネント（パーツ・雛形）単位で Web を作るための準備を最短でやることを目標として、Riot.js の使い方などをできるだけカンタンに紹介しようとおもいます！

## <a id="just-script">jQuery 同様、`script` で読み込むだけ！</a>

今回、この記事とサンプルの中では、webpack などの JavaScript のモジュールバンドラーは使いません。
Node.js をインストールして、`npm install` みたいな一連の環境構築も必要ありません。

`script` で JavaScript を読み込むだけ。 jQuery と一緒です。
基本的に、Riot.js の単体の機能はすべて `script` で読み込むだけで使うことができます。

[公式のドキュメントでは **「インブラウザ・コンパイル」** と紹介](https://riot.js.org/ja/compiler/#%E3%82%A4%E3%83%B3%E3%83%96%E3%83%A9%E3%82%A6%E3%82%B6%E3%82%B3%E3%83%B3%E3%83%91%E3%82%A4%E3%83%AB
)されている使い方です。

※
`.riot` の中で SASS などと組み合わせて使いたい場合などは別途そういった開発環境が必要です。
webpack などを使った開発環境に Riot.js を組み込むことももちろん可能ですが、今回はなるべく簡単に始めることを目的としているのでそのへんは省略します。

これ以降のサンプルファイルは、[この Github のリポジトリ](https://github.com/nibushibu/rakuraku-riot)を参考にしてください。

というわけで早速…

## <a id="sample-1">サンプル 1: とりあえず使ってみる</a>

とりあえず Riot.js でコンポーネントを作ってみます。

`sample-1` というフォルダの中身をご覧ください。
`index.html` と `hello-riot.riot` という 2 つのファイルがあります。

後者の `.riot` という拡張子のファイルは、Riot.js 独自のファイル形式ですが、その実態はほとんど通常の HTML そのままです。
特定の部品に必要な HTML / CSS / JavaScript を、この `.riot` という1ファイルにまとめておいて、それを HTML 側から呼び出して使います。

では、ファイルの中身を見てみましょう。

かなりシンプルなものですが、一応それぞれ解説をコメントでつけてみました。

**sample-1/index.html**

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>ラクラク Riot.js サンプル 1</title>
</head>
<body>

  <!-- hello-riot というオリジナルのタグ（コンポーネント）を配置 -->
  <hello-riot data-riot></hello-riot>

  <!-- hello-riot を定義した .riot ファイルを読み込み -->
  <script type="riot" data-src="./hello-riot.riot"></script>

  <!-- Riot.js（コンパイラ含むバージョン） を CDN から読み込み -->
  <script src="https://unpkg.com/riot@4/riot+compiler.min.js"></script>

  <script>
    // 読み込まれた .riot をコンパイル（内部で実行可能な JavaScript に変換）
    riot.compile()

    // コンパイルが終わったら
    .then(() => {

      // data-riot という属性値を持つタグをコンポーネントと認識して展開（マウント）
      // data-riot 属性がついたタグ名と、.riot のファイル名が対応しています（この例では hello-riot）
      riot.mount('[data-riot]')
    })
  </script>
</body>
</html>
```

**sample-1/hello-riot.riot**

```riot
<hello-riot>
  <p>ハロー！暴動！🔥</p>
</hello-riot>
```

👉 [実際に動いているサンプル](https://codesandbox.io/s/rakuraku-riot-sample-1-4cprl)がこちら。

`herro-riot.riot` に書いた HTML が、index.html のほうに展開（＝マウント）されてます。

…はい、どうでしょう？カンタンですよね!？ 👍
<br>いわゆる「黒い画面」も出てきません。
<br>jQuery と同じように、スクリプト本体を CDN とかから読み込んで、jQuery プラグインを読み込むように、`.riot` ファイルを読み込むだけです。

### ⚠️ ローカルで確認するときの注意点

さあ、ではさっそくローカルにも Github からデータをダウンロードして `sample-1/index.html` をブラウザで開いてみましょう。
<br>**…と、ここにひとつちょっとした落とし穴があります。**
<br>ローカルで `sample-1/index.html` をブラウザで開くと、なぜか画面は真っ白。。。

#### CORS制限の都合で、httpでアクセスできるサーバーが必要。

最近のブラウザにはセキュリティ上、クロスドメイン通信の制限があって、ローカルでデータを開いていると JavaScript が外部のファイルを読み込みにいけません。
（つまり、この制限のせいで、ローカル環境だと Riot.js 本体のスクリプトが、.riot ファイルを読み込むことができない）

なので、なにかしら動作確認には http 通信でアクセスできるサーバー環境が必要です。

いくつか方法はありますが、手軽な方法だと

- 手持ちの適当なサーバー環境にデータをアップして確認する
- [MAMP](https://www.mamp.info/) などを使ってローカルのサーバー環境に置いて確認する

とかかなと思います。

とにかく、`file://` というローカルファイルを直接開くかたちだとエラーになってしまうので、そこだけご注意ください。


## <a id="sample-2">サンプル 2: `.riot` の中に CSS を書く</a>

とりあえず、サンプル 1 で、部品（コンポーネント）を `.riot` という外部ファイルにして、それを読み込むことができました。

ただ、このままではまだ HTML の一部を外部においただけなので、では次にその中にCSSを書いていきます。

👉 [サンプルの確認はこちら](https://codesandbox.io/s/rakuraku-riot-sample-2-gp0gu)から。

**sample-2/styled-text.riot**

```riot
<styled-text>
  <div class="foo">暴動をスタイリング🔥</div>

  <style>
    .foo {
      display: inline-block;
      background-color: #ffa9a9;
      border-radius: 4px;
      padding: 0.5em;
    }
  </style>
</styled-text>
```

これもかんたんですね。素の HTML / CSS と一緒です。

少し違うのは、`.riot` の中に書いたスタイルは、コンパイルされるときに以下のように **そのタグの中だけのスタイルに書き換えられます。**

```css
/* .riot に記述したスタイル */

.foo {
  border-radius: 4px;
}

/* コンパイル後、実際にブラウザに反映されるスタイル */

styled-text .foo,
[is="styled-text"] .foo {
  border-radius: 4px;
}
```

### スタイルを `.riot` の中に書くかどうかは好みで決めていい。

`.riot` の中にスタイルを書くことで、部品ごとに関連する HTML / CSS / JavaScript を1ファイルにまとめることができて管理も楽になりますが、人によっては、スタイルは別途 CSS に書きたいという人もいると思います。（自分）

`.riot` にまとめた HTML は、マウントされれば、当然、HTML に直書きされた要素と同じように、`link` で読み込まれた css のスタイルが適応されるので、無理に `.riot` にスタイルを書く必要はありません。

好みや管理の都合に合わせて使えば良いと思います。

### `.riot` の中で SASS とか使うなら開発環境の準備が必須

スタイルの記述に SASS とか PostCSS（Autoprefixer） とかを使うには、プリコンパイル（.riot を事前にJavaScriptに変換しておく）が必要で、Node.js やモジュールバンドラーなどの開発環境が必要になります。

自分も実制作で使うときはそういったモジュールバンドラー含む環境を準備していますが、今回はまずかんたんに始めることを目的としているので、この記事では割愛します。

## <a id="sample-3">サンプル 3: `.riot` の中に JavaScript を書く</a>

今度は、`.riot` の中に JavaScript を書いていきます。

また、`.riot` の中では HTML の任意の場所に `{}` を使って、JavaScript の変数などを差し込んだり、`onclick` 属性などで任意の関数を実行するイベントを付加することができます。

👉 [動作サンプル](https://codesandbox.io/s/rakuraku-riot-sample-3-cj0fo)

```riot
<riot-fire>
  <p>
    <button class="foo" onclick="{ burn }">暴動を起こす</button>
  </p>
  <p>{ state.fire }</p>

  <script>
    export default {
      state: {
        fire: '🔥'
      },
      // button をクリック（onclick）したときに実行する関数
      burn(){

        // state.fire という文字列に🔥を追加
        this.state.fire += '🔥'

        // このタグを更新。実際のブラウザに見えるHTMLが更新される。
        this.update()
      }
    }
  </script>
</riot-fire>
```

jQuery だと `$('button').on('click', ()=>{ ... })` みたいなことを書くところだと思いますが、

Riot.js では、このように、 HTML の中に PHP のように JavaScript の変数や処理などを埋め込むことができるので、見た目としての HTML と、JavaScript 側の処理をより完結に記述することができます。

ちなみに、`export default {...}` という書式になっている部分は、JavaScript モジュールの書式で、jQuery の利用ではあまり目にしないと思いますが、ひとまずはお約束というか、こういうものだと理解して使えばOKです。

### `state` は、そのコンポーネントの変数を保持する場所

Riot.js は他の UI ライブラリやフレームワークに比べれば独自の構文やルールは非常に少ない、薄いライブラリですが、それでも、いくつか覚えておく必要のある機能や概念があります。

`state` もその一つです。

サンプル 3 の下記の部分ですね。

```js
export default {
  state: {
    fire: '🔥'
  }
}
```

上記のように `state: { 変数名: 値, 変数名: 値 }` というようなオブジェクトの書式で、そのコンポーネントだけが参照できる任意の変数を設定できます。

サンプル 3 では、画面上に表示する絵文字の炎（🔥）を `state.fire` （state の中の fire という変数）の中に文字列として持っています。

`sample-3/riot-fire.riot` 5行目の

```html
<p>{ state.fire }</p>
```

この部分で、の変数の値を HTML 上に表示しています。

ボタンをクリックするごとに、この `state.fire` に🔥という文字を加えることで、HTML上に🔥の絵文字が増える、という流れになっています。

### `this.update()` で見た目を更新する

Riot.js は単方向データバインディングを採用していて、`state.fire` の値を変更したあと、HTML にその変更を反映するには、コンポーネント（つまり自分自身）に対して `update()` を実行する必要があります。

サンプル 3 の例で、以下のように書いた部分ですね。（解説コメント省略）

```js
burn(){
  this.state.fire += '🔥'
  this.update()
}
```

ちなみに、こういうふうにも書けます。（結果は一緒）

```js
burn(){
  this.update({ fire: this.state.fire + '🔥' })
}
```

参考: [公式サイト ドキュメンテーション > 状態](https://riot.js.org/ja/documentation/#%E7%8A%B6%E6%85%8B)


## <a id="sample-4">サンプル 4: `if` ディレクティブを使って HTML を出し分けてみる</a>

`.riot` の HTML の部分に `if` という属性値をつけることで、HTML 要素を一定の条件化でだけ表示したり削除したりすることができます。

👉 [動作サンプル](https://codesandbox.io/s/rakuraku-riot-sample-4-m78mp)

```riot
<toggle-comment>
  <p>
    <button onclick="{ toggle }">Riot is ...</button>
  </p>
  <!-- 以下の div は state.contentVisible が true の時だけ表示される -->
  <div if="{ state.contentVisible }" class="box">
    🔥暴動🔥
  </div>

  <style>
    /* 中略 */
  </style>

  <script>
    export default {
      state: {
        contentVisible: false
      },
      toggle() {
        this.update({ contentVisible: !this.state.contentVisible })
      }
    }
  </script>
</toggle-comment>
```

jQuery だと、`$('div').show()` とか `$('div').hide()` とかやる感じですかね。

こういう簡単なサンプルだとあまり Riot.js の恩恵を実感できないかもしれませんが、大事なのは、見た目の HTML と、JavaScript のロジックを整理して書けるということです。

作るものが複雑になってくるにつれて、jQuery で書いていると「このステータスの時は HTML のどの部分をどうする」みたいな記述がどんどん増えてしまいます。

Riot.js のように、ステータスは `state` の中の変数などでシンプルに持って、HTML 側にはこの `if` ディレクティブや、後述のクラス指定の出し分けなどを使うことで、つくる UI が複雑になってきても効率的に記述できます。

## <a id="sample-5">サンプル 5: 三項演算子で `class` 属性値を出し分ける</a>

Riot.js は、独自の構文が非常に少ないライブラリです。

`if` ディレクティブと、後ほど説明する `each` （データに合わせて HTML を量産する時に利用）しか、ほぼ独自の構文は存在しません。
（厳密には、構文以外に、ライフサイクルメソッドなど、その他にも、もう少し覚えることはありますが、それでも他のライブラリに比べればすごく少ない）

その代わり、`.riot` の中では HTML部分の `{}` の中は基本的に通常の JavaScript として評価されるので、JavaScript の書き方を工夫することで、色々なことができるようになります。

よくあるのは、HTML の `class` の内容を、状況に合わせて変更したりするケースです。

いくつかやり方がありますが、手っ取り早いのは 三項演算子 を使うパターンです。

WIP