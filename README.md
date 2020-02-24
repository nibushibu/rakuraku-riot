# jQuery で忙しい人のための Riot.js

jQuery、なんだかんだ楽チンですよね。

jQuery は長らく Web フロントエンドの「魔法の杖」でしたが、数年前に SPA に強い次世代のライブラリやフレームワークが登場して以降は、jQuery はもう過去のモノ、みたいに言われることもあります。

たしかに、ある意味ではそうかもしれません。
jQuery の機能の一部は、現在では素の JavaScript でできるようになりましたし、最近のフレームワークに比べれば jQuery は複雑な UI を作り込むのにはあまり向いていません。

だけど、小規模な Web 制作や単発の機能の開発であれば、プラグインも豊富で、何より `script` で読み込むだけで簡単に導入できる jQuery が生きるシーンもあると思います。

## Riot.js はそんな風に思っているあなたのためのライブラリです

[Riot.js](https://riot.js.org/ja/) は、近年日本でもユーザーが急増している Vue.js などと同じく、HTML / CSS / JavaScript といった Web の見た目に関わる部分を効率的に管理・制作するための JavaScript ライブラリです。

[Riot.js — Simple and elegant component-based UI library · Riot.js](https://riot.js.org/ja/)

この記事では、Riot.js をつかってコンポーネント（パーツ・雛形）単位で Web を作るための準備を最短でやることを目標として、Riot.js の使い方などをさっくり紹介したいと思います。

## jQuery 同様、`script` で読み込むだけ。

今回、この記事とサンプルの中では、webpack などの JavaScript のモジュールバンドラーは使いません。
Node.js をインストールして、`npm install` みたいな一連の環境構築も必要ありません。

`script` で JavaScript を読み込むだけ。 jQuery と一緒です。
基本的に、Riot.js の単体の機能はすべて `script` で読み込むだけで使うことができます。

※
SASS などと組み合わせて使いたい場合などは別途そういった開発環境が必要です。
webpack などを使った開発環境に Riot.js を組み込むことももちろん可能ですが、今回はなるべく簡単に始めることを目的としているのでそのへんは省略します。

これ以降のサンプルファイルは Github にファイルをまとめていますので参考にしてみてください。

https://github.com/nibushibu/rakuraku-riot

## サンプル 1 : とりあえず使ってみる

とりあえず Riot.js でコンポーネントを作ってみます。

`sample-1` というフォルダの中身へ移動してみましょう。
`index.html` と `hello-riot.riot` という 2 つのファイルがあります。
中身はこんなそれぞれ以下のような感じです。

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

```riot
<hello-riot>
  <p>ハロー！暴動！🔥</p>
</hello-riot>
```