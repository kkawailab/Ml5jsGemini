
# 第3章: リアルタイム画像認識 - Webカメラで世界を認識する

前の章では、静的な一枚の画像をAIに認識させました。この章ではさらに一歩進んで、Webカメラの映像を使い、リアルタイムで目の前にあるものをAIに認識させてみましょう。

## 1. Webカメラとの連携

`ml5.js` は、`p5.js` と組み合わせることで、Webカメラの映像を簡単に扱うことができます。`p5.js` の `createCapture(VIDEO)` という関数を使うと、数行のコードでカメラを起動し、その映像をWebページに表示できます。

リアルタイム認識の基本的な流れは以下のようになります。

1.  Webカメラで映像の1フレーム（静止画）を取得する。
2.  そのフレームを `imageClassifier` に渡して、何が写っているか認識させる。
3.  結果を画面に表示する。
4.  1〜3を高速で繰り返す。

これにより、あたかもAIが「今、目の前にあるもの」をリアルタイムで認識しているかのような体験ができます。

## 2. リアルタイム画像認識のサンプルコード

それでは、`index.html` を以下のコードに書き換えてください。
このコードを実行すると、ブラウザがカメラへのアクセス許可を求めてきますので、「許可」を選択してください。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>第3章 | リアルタイム画像認識</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.0/p5.js"></script>
  <script src="https://unpkg.com/ml5@latest/dist/ml5.min.js"></script>
</head>
<body>
  <h1>リアルタイム画像認識</h1>
  <p>Webカメラに写ったものは何でしょう？</p>
  <script>
    let classifier;
    let video; // Webカメラの映像を格納する変数
    let label = '判定中...'; // 認識結果のラベル
    let confidence = ''; // 認識結果の確信度

    function preload() {
      // MobileNetモデルで画像分類器を初期化
      classifier = ml5.imageClassifier('MobileNet');
    }

    function setup() {
      // 640x480ピクセルのキャンバスを作成
      createCanvas(640, 480);
      // Webカメラからの映像を取得
      video = createCapture(VIDEO);
      // 映像の表示サイズをキャンバスに合わせる
      video.size(640, 480);
      // 元の映像は非表示にする（キャンバスに描画するため）
      video.hide();

      // 映像の分類を開始する
      classifyVideo();
    }

    // p5.jsのdraw関数。繰り返し実行される
    function draw() {
      // キャンバスにビデオフレームを描画
      image(video, 0, 0);

      // 認識結果のテキストを描画
      fill(255); // 文字色を白に
      textSize(16); // 文字サイズを16ピクセルに
      textAlign(CENTER); // 文字を中央揃えに
      // 結果を画面下部に表示
      text(label + ' ' + confidence, width / 2, height - 4);
    }

    // 映像を繰り返し分類するための関数
    function classifyVideo() {
      // ビデオフレームを分類器にかける
      classifier.classify(video, gotResult);
    }

    // 分類結果を受け取る関数
    function gotResult(error, results) {
      if (error) {
        console.error(error);
        return;
      }
      // 結果をグローバル変数に保存
      label = results[0].label;
      confidence = nf(results[0].confidence, 0, 2);
      
      // 再度、分類を実行してループさせる
      classifyVideo();
    }
  </script>
</body>
</html>
```

### 実行してみよう

このHTMLファイルをブラウザで開くと、Webカメラの映像が表示され、その映像の下部にAIによる認識結果がリアルタイムで表示されます。スマートフォンやマグカップなど、身の回りのものをカメラに見せて、AIが正しく認識できるか試してみましょう。

`classifyVideo()` 関数の中で、結果を受け取った後に再度 `classifyVideo()` を呼び出しているのがポイントです。これにより、処理が終わり次第、次の認識が始まり、効率的なリアルタイム処理が実現できています。

---

## 練習問題

### 問題1

認識結果を表示しているテキストの色を白から赤色に、文字サイズを16ピクセルから24ピクセルに変更してみましょう。（ヒント: `draw` 関数の中の `fill()` と `textSize()` を変更します）

### 問題2

AIの判断が曖昧なときは結果を表示しないようにしてみましょう。具体的には、確信度（confidence）が `0.5` より大きい場合のみ、ラベルと確信度を更新するように `gotResult` 関数を改造してください。

---

## 解答

### 解答1

`draw` 関数を以下のように変更します。`fill()` の引数を `(255, 0, 0)` にすると赤色になります。

```javascript
function draw() {
  image(video, 0, 0);

  // 文字色を赤に、サイズを24に変更
  fill(255, 0, 0);
  textSize(24);
  textAlign(CENTER);
  text(label + ' ' + confidence, width / 2, height - 4);
}
```

### 解答2

`gotResult` 関数を以下のように変更します。`if` 文を追加して、確信度をチェックします。

```javascript
function gotResult(error, results) {
  if (error) {
    console.error(error);
    return;
  }

  // 確信度が0.5より大きい場合のみ結果を更新する
  if (results[0].confidence > 0.5) {
    label = results[0].label;
    confidence = nf(results[0].confidence, 0, 2);
  }
  
  classifyVideo();
}
```
