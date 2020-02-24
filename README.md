# jQuery で忙しい人のための Riot.js を最短で使うための手引き

jQuery、なんだかんだ楽チンですよね。

jQuery は長らく Web フロントエンドの「魔法の杖」でしたが、数年前に SPA に強い次世代のライブラリやフレームワークが登場して以降は、jQuery はもう過去のモノ、みたいに言われることもあります。

たしかに、ある意味ではそうかもしれません。
jQuery の機能の一部は、現在では素の JavaScript でできるようになりましたし、最近のフレームワークに比べれば jQuery は複雑な UI を作り込むのにはあまり向いていません。

だけど、小規模な Web 制作や単発の機能の開発であれば、プラグインも豊富で、何より `script` で読み込むだけで簡単に導入できる jQuery が生きるシーンもあると思います。

## Riot.js はそんな風に思っているあなたのためのライブラリ。

[Riot.js](https://riot.js.org/ja/) は、近年日本でもユーザーが急増している Vue.js などと同じく、HTML / CSS / JavaScript といった Web の見た目に関わる部分を効率的に管理・制作するための JavaScript ライブラリです。

[Riot.js — Simple and elegant component-based UI library · Riot.js](https://riot.js.org/ja/)

そこで、Riot.js をつかってコンポーネント（パーツ・雛形）単位で Web を作るための準備を最短でやることを目標として、Riot.js の使い方などをできるだけカンタンに紹介しようとおもいます！

## jQuery 同様、`script` で読み込むだけ！

今回、この記事とサンプルの中では、webpack などの JavaScript のモジュールバンドラーは使いません。
Node.js をインストールして、`npm install` みたいな一連の環境構築も必要ありません。

`script` で JavaScript を読み込むだけ。 jQuery と一緒です。
基本的に、Riot.js の単体の機能はすべて `script` で読み込むだけで使うことができます。

[公式のドキュメントでは **「インブラウザ・コンパイル」** と紹介](https://riot.js.org/ja/compiler/#%E3%82%A4%E3%83%B3%E3%83%96%E3%83%A9%E3%82%A6%E3%82%B6%E3%82%B3%E3%83%B3%E3%83%91%E3%82%A4%E3%83%AB
)されている使い方です。


※
SASS などと組み合わせて使いたい場合などは別途そういった開発環境が必要です。
webpack などを使った開発環境に Riot.js を組み込むことももちろん可能ですが、今回はなるべく簡単に始めることを目的としているのでそのへんは省略します。

これ以降のサンプルファイルは、[この Github のリポジトリ](https://github.com/nibushibu/rakuraku-riot)を参考にしてください。

というわけで早速…

## サンプル 1 : とりあえず使ってみる

とりあえず Riot.js でコンポーネントを作ってみます。

`sample-1` というフォルダの中身をご覧ください。
`index.html` と `hello-riot.riot` という 2 つのファイルがあります。

後者の `.riot` という拡張子のファイルは、Riot.js 独自のファイル形式ですが、その実態はほとんど通常の HTML そのままです。
特定の部品に必要は HTML / CSS / JavaScript を、この `.riot` という1ファイルにまとめておいて、それを HTML 側から呼び出して使います。

では、ファイルの中身を見てみましょう。

かなりシンプルなものですが、一応それぞれ解説をコメントでつけてみました。

**sample-1/index.html**

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>ラクラク Riot.js サンプル1</title>
</head>
<body>

  <!-- hello-riot というオリジナルのタグ（コンポーネント）を配置 -->
  <hello-riot data-riot></hello-riot>

  <!-- hello-riot を定義した .riot ファイルを読み込み -->
  <script type="riot" data-src="./hello-riot.riot"></script>

  <!-- Riot.js（コンパイラ含むバージョン） を CDN から読み込み -->
  <script src="https://unpkg.com/riot@4/riot+compiler.min.js"></script>

  <script>
    // 読み込まれた .riot をコンパイル（内部で実行可能なJavaScriptに変換）
    riot.compile()

    // コンパイルが終わったら
    .then(() => {

      // data-riot という属性値を持つタグをコンポーネントと認識して展開（マウント）
      // data-riot属性がついたタグ名と、.riot のファイル名が対応しています（この例では hello-riot）
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

### ローカルで確認するときの注意点

さあ、ではさっそくローカルもに Github からデータをダウンロードして `sample-1/index.html` をブラウザで開いてみましょう。
<br>**…と、ここにひとつちょっとした落とし穴があります。**
<br>ローカルで `sample-1/index.html` をブラウザで開くと、なぜか画面は真っ白。。。

#### CORS制限の都合で、httpでアクセスできるサーバーが必要。

最近のブラウザにはセキュリティの安全上、クロスドメイン通信の制限があって、ローカルでデータを開いていると JavaScript が外部のファイルを読み込みにいけません。
（つまり、この制限のせいで、ローカル環境だと Riot.js 本体のスクリプトが、.riot ファイルを読み込むことができない）

なので、なにかしら動作確認には http 通信でアクセスできるサーバー環境が必要です。

いくつか方法はありますが、手軽な方法だと

- 手持ちの適当なサーバー環境にデータをアップして確認する
- [MAMP](https://www.mamp.info/) などを使ってローカルのサーバー環境に置いて確認する

とかかなと思います。

とにかく、`file://` というアドレスから開いてもエラーになってしまうので、そこだけご注意ください。

## サンプル 2 : `.riot` の中に CSS を書く

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

### スタイルをタグの中に書くかどうかは好みで決めていい。

`.riot` の中にスタイルを書くことで、部品ごとに関連する HTML / CSS / JavaScript を1ファイルにまとめることができて管理も楽になりますが、人によっては、スタイルは別途 CSS に書きたいという人もいると思います。（自分）

`.riot` にまとめた HTML は、マウントされれば、当然、HTML に直書きされた要素と同じように読み込まれた css のスタイルが適応されるので、無理に `.riot` にスタイルをまとめる必要はありません。

好みや管理の都合に合わせて使えば良いと思います。

### `.riot` の中で SASS とか使うなら開発環境準備が必須

スタイルの記述に SASS とか PostCSS（Autoprefixer） とかを使うには、プリコンパイル（.riot を事前にJavaScriptに変換しておく）が必要で、Node.js やモジュールバンドラーなどの開発環境が必要になります。

自分も実制作で使うときはそういったモジュールバンドラー含む環境を準備していますが、今回はまずかんたんに始めることを目的としているので、この記事では割愛します。

## サンプル 3 : `.riot` の中に JavaScript を書く

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

Riot.js では、このように、 HTML の中に PHP のテンプレート構文のように JavaScript の変数や処理などを埋め込むことができるので、見た目としての HTML と、JavaScript 側の処理をより完結に記述することができます。

ちなみに、`export default {...}` という書式になっている部分は、JavaScript モジュールの書式で、jQuery の利用ではあまり目にしないと思いますが、ひとまずはお約束というか、こういうものだと理解して使えばOKです。
