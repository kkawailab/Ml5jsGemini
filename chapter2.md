
# 第2章: 画像認識 - 画像に何が写っているか判定する

この章では、AIの代表的な技術である「画像認識」に挑戦します。`ml5.js` を使って、画像に何が写っているのかをAIに当ててもらいましょう。

## 1. 画像認識とは？

画像認識（Image Classification）とは、AIが画像を見て、その中に写っている「主要なもの」が何かを判断する技術です。例えば、猫の画像を見せると「これは猫です」と教えてくれます。

`ml5.js` では、AIは判断結果を「ラベル（名前）」と「確信度（Confidence）」のペアで返してくれます。確信度とは、AIがその判断にどれくらい自信を持っているかを示す0から1の間の数値です。1に近いほど、自信があることを意味します。

## 2. `imageClassifier` を使ってみよう

`ml5.js` で画像認識を行うには、`imageClassifier()` という関数を使います。この関数は、たくさんの画像でトレーニングされた「学習済みモデル」を読み込んでくれます。

今回は、`MobileNet` という、高速で動作する人気のモデルを使います。

### サンプルコード

第1章で作成した `index.html` を、以下のように書き換えてください。今回は、Web上にある犬の画像をAIに認識させてみます。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>第2章 | 画像認識</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.0/p5.js"></script>
  <script src="https://unpkg.com/ml5@latest/dist/ml5.min.js"></script>
</head>
<body>
  <h1>画像認識デモ</h1>
  <p>この画像は一体何でしょう？</p>
  <!-- AIに認識させたい画像 -->
  <img id="image" src="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fb/Welshcorgipembroke.JPG/800px-Welshcorgipembroke.JPG" width="400" crossorigin="anonymous" />
  <p>AIの判定結果:</p>
  <p id="result"></p>
  <p>確信度:</p>
  <p id="confidence"></p>

  <script>
    // 変数の準備
    let classifier;
    let img;
    let resultP;
    let confidenceP;

    // p5.jsのpreload関数。setupの前に実行される。
    // モデルや画像などの重いデータを先に読み込んでおくのに使う。
    function preload() {
      // MobileNetモデルで画像分類器を初期化する
      classifier = ml5.imageClassifier('MobileNet');
      // HTMLのimg要素を取得する
      img = document.getElementById('image');
    }

    // p5.jsのsetup関数。最初に一度だけ実行される
    function setup() {
      // キャンバスは今回は使わないので作成しない
      noCanvas();
      
      // 結果を表示するためのp要素を取得
      resultP = document.getElementById('result');
      confidenceP = document.getElementById('confidence');

      // 画像の分類を実行する
      // 第1引数: 対象の画像要素
      // 第2引数: 分類が終わったときに呼ばれる関数(コールバック関数)
      classifier.classify(img, gotResult);
    }

    // 分類結果を受け取る関数
    function gotResult(error, results) {
      // エラーがあればコンソールに表示して処理を中断
      if (error) {
        console.error(error);
        return;
      }

      // 結果(results)は確信度の高い順に並んだ配列になっている
      // results[0] が最も確信度の高い結果
      console.log(results);

      // 結果をHTMLに表示する
      resultP.innerText = results[0].label;
      // toFixed(2)で小数点以下2桁にフォーマットする
      confidenceP.innerText = nf(results[0].confidence, 0, 2);
    }
  </script>
</body>
</html>
```

### 実行結果の確認

このHTMLファイルをブラウザで開くと、コーギーの画像の下に、AIの判定結果が表示されます。おそらく「Pembroke, Pembroke Welsh corgi」のような結果が表示され、確信度は0.9以上に近い値になるはずです。

`crossorigin="anonymous"` は、他のサイトから画像を読み込む際に必要なセキュリティ上のおまじないです。

---

## 練習問題

### 問題1

サンプルコードの `<img>` タグの `src` 属性を、あなたが好きな動物や乗り物などの画像URLに変更して、AIが正しく認識できるか試してみましょう。（ヒント: Google画像検索などで見つけた画像のURLを使ってみましょう）

### 問題2

現在のコードでは、最も確信度の高い結果1つだけを表示しています。これを、上位3つの結果（ラベルと確信度）をすべて表示するように変更してみましょう。（ヒント: `gotResult` 関数の `results` は配列です。forループを使うと便利です。）

---

## 解答

### 解答1

HTMLの `<img>` タグの `src` を変更します。例えば、猫の画像にする場合は以下のようになります。

```html
<img id="image" src="https://upload.wikimedia.org/wikipedia/commons/thumb/4/4d/Cat_November_2010-1a.jpg/800px-Cat_November_2010-1a.jpg" width="400" crossorigin="anonymous" />
```

### 解答2

`gotResult` 関数と、HTMLの表示部分を少し変更します。

**HTMLの変更箇所:**
結果を複数表示できるように、`<p>` タグを `<div>` タグに変更します。

```html
<p>AIの判定結果:</p>
<div id="result"></div>
```

**JavaScript (`gotResult`関数) の変更箇所:**
forループを使って、結果を3回画面に追加します。

```javascript
function gotResult(error, results) {
  if (error) {
    console.error(error);
    return;
  }
  
  // 結果を表示するdiv要素を取得
  const resultDiv = document.getElementById('result');
  // 元の内容をクリア
  resultDiv.innerHTML = '';

  // 上位3件の結果をループで処理する
  for (let i = 0; i < 3; i++) {
    const label = results[i].label;
    const confidence = nf(results[i].confidence, 0, 2);
    
    // 新しいp要素を作成して結果を追加
    const p = document.createElement('p');
    p.innerText = `${i + 1}: ${label} (確信度: ${confidence})`;
    resultDiv.appendChild(p);
  }
}
```
