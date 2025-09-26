
# 第1章: はじめの一歩 - AIとml5.js、環境準備

AIプログラミングの世界へようこそ！この章では、AIや `ml5.js` がどのようなものかを学び、実際にプログラムを動かすための準備をします。

## 1. 人工知能（AI）とは？

人工知能（AI）とは、一言でいうと「コンピュータが人間のように学習したり、判断したりするための技術」のことです。例えば、写真に写っているのが犬か猫かを判断したり、文章の意味を理解したり、ゲームで最適な次の一手を考えたりと、様々な場面でAIは活躍しています。

このチュートリアルでは、特に身近な「画像認識」などの分野を中心にAIを体験していきます。

## 2. ml5.js とは？

`ml5.js` は、Webブラウザ上で簡単にAIの機能を使えるようにしてくれるJavaScriptライブラリです。通常、AIプログラミングには複雑な数学の知識や高性能なコンピュータが必要ですが、`ml5.js` はそれらをすべて裏側で処理してくれます。

私たちは、簡単な命令（関数）を呼び出すだけで、学習済みのAIモデル（たくさんのデータで賢くなったAI）の力を借りることができます。

このチュートリアルでは、`ml5.js` をより簡単に扱うために、`p5.js` という別のライブラリも一緒に使います。`p5.js` は、図形を描画したり、アニメーションを作成したりするのが得意なライブラリで、AIの処理結果を画面に表示するのにとても便利です。

## 3. 開発環境の準備

特別なソフトウェアのインストールは不要です。以下の2つがあれば準備OKです。

*   **テキストエディタ:** メモ帳や、VSCode、Sublime Textなど、コードを書くためのエディタ。
*   **Webブラウザ:** Google ChromeやFirefoxなど、プログラムの実行結果を確認するためのブラウザ。

### HTMLファイルの作成

まず、作業用のフォルダ（このチュートリアルファイルがある場所）に、`index.html` という名前のファイルを作成し、以下の内容をコピー＆ペーストしてください。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>ml5.js チュートリアル</title>
  <!-- p5.jsライブラリを読み込む -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.0/p5.js"></script>
  <!-- ml5.jsライブラリを読み込む -->
  <script src="https://unpkg.com/ml5@latest/dist/ml5.min.js"></script>
</head>
<body>
  <h1>ml5.jsチュートリアル</h1>
  <script>
    // ここにJavaScriptのコードを書いていきます
  </script>
</body>
</html>
```

## 4. はじめてのml5.jsプログラム

`ml5.js` が正しく読み込めているか確認してみましょう。
先ほど作成した `index.html` の `<script>` タグの中に、以下のコードを追加してください。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>ml5.js チュートリアル</title>
  <!-- p5.jsライブラリを読み込む -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.0/p5.js"></script>
  <!-- ml5.jsライブラリを読み込む -->
  <script src="https://unpkg.com/ml5@latest/dist/ml5.min.js"></script>
</head>
<body>
  <h1>ml5.jsチュートリアル</h1>
  <script>
    // p5.jsのsetup関数。最初に一度だけ実行される
    function setup() {
      // コンソールにml5.jsのバージョン情報を表示する
      // これにより、ライブラリが正しく読み込まれているか確認できる
      console.log('ml5 version:', ml5.version);
    }
  </script>
</body>
</html>
```

この `index.html` ファイルを保存し、Webブラウザで開いてみてください。
画面には「ml5.jsチュートリアル」と表示されるだけですが、ブラウザの開発者ツール（Chromeなら `Ctrl+Shift+I` または `Cmd+Opt+I`）を開き、「Console」タブを確認すると、以下のようにバージョン情報が表示されているはずです。

```
ml5 version: (バージョン番号)
```

これが確認できれば、準備は完了です！

---

## 練習問題

### 問題1

`ml5.js` のバージョンを表示したように、`p5.js` のバージョンもコンソールに表示させてみましょう。（ヒント: `p5.VERSION` という変数にバージョン情報が格納されています）

### 問題2

HTMLの `<h1>` タグに表示されているテキストを、「AIプログラミングをはじめよう」という文章に変更してみましょう。

---

## 解答

### 解答1

`setup` 関数の中に `console.log('p5.js version:', p5.VERSION);` を追加します。

```javascript
function setup() {
  // ml5.jsのバージョン情報を表示
  console.log('ml5 version:', ml5.version);
  // p5.jsのバージョン情報を表示
  console.log('p5.js version:', p5.VERSION);
}
```

### 解答2

HTMLファイルの `<body>` タグの中を以下のように変更します。

```html
<body>
  <h1>AIプログラミングをはじめよう</h1>
  <script>
    // ... (JavaScriptコードは同じ)
  </script>
</body>
```
