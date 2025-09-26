
# 第4章: 物体検出 - 画像の中の物体を見つけ出す

これまでの画像認識では、画像全体に「何が」写っているかをAIに判断させていました。この章で学ぶ「物体検出」はさらにパワフルです。画像の中の「どこに」「何が」あるのかを、複数同時に見つけ出すことができます。

## 1. 物体検出（Object Detection）とは？

画像認識（Image Classification）と物体検出（Object Detection）の違いをおさらいしましょう。

*   **画像認識:** 画像に写っている最も主要なものを1つだけ当てる。（例：「この画像は猫です」）
*   **物体検出:** 画像に写っている複数の物体それぞれの位置と名前を当てる。（例：「左上に猫がいて、右下に犬がいます」）

物体検出では、AIは検出した物体を「バウンディングボックス」と呼ばれる四角い枠で囲み、その枠の位置（x座標, y座標, 幅, 高さ）と、物体の名前（ラベル）、そして確信度を教えてくれます。

## 2. `objectDetector` を使ってみよう

`ml5.js` で物体検出を行うには `objectDetector()` という関数を使います。今回は、Webでよく使われる `cocossd` という学習済みモデルを利用します。このモデルは、人、車、犬、猫、コップなど、約80種類の身近な物体を検出できます。

### サンプルコード

今回は、複数の物体が写っている静的な画像を使って、物体検出を試してみましょう。`index.html` を以下のコードに書き換えてください。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>第4章 | 物体検出</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.0/p5.js"></script>
  <script src="https://unpkg.com/ml5@latest/dist/ml5.min.js"></script>
</head>
<body>
  <h1>物体検出デモ</h1>
  <p>画像の中の物体を見つけ出します。</p>
  <script>
    let objectDetector;
    let img;
    let objects = []; // 検出結果を格納する配列

    function preload() {
      // 検出させたい画像を読み込む
      img = loadImage('https://upload.wikimedia.org/wikipedia/commons/thumb/3/3a/Road_in_Norway.jpg/1024px-Road_in_Norway.jpg');
      // cocossdモデルで物体検出器を初期化
      objectDetector = ml5.objectDetector('cocossd');
    }

    function setup() {
      // 画像と同じサイズのキャンバスを作成
      createCanvas(img.width, img.height);
      
      // 物体検出を実行
      // 第1引数: 対象の画像
      // 第2引数: 検出が終わったときに呼ばれる関数
      objectDetector.detect(img, gotResult);
    }

    // p5.jsのdraw関数。setupの後に一度だけ実行される（noLoop()のため）
    function draw() {
      // 背景に画像を一度だけ描画
      image(img, 0, 0, width, height);

      // 検出されたすべての物体に対してループ処理
      for (let i = 0; i < objects.length; i++) {
        // 枠線の色を黒に設定
        stroke(0, 0, 0);
        // 枠線の太さを4ピクセルに
        strokeWeight(4);
        // 四角形の中を塗りつぶさない
        noFill();
        // 四角形（バウンディングボックス）を描画
        rect(objects[i].x, objects[i].y, objects[i].width, objects[i].height);

        // ラベルテキストの準備
        noStroke(); // テキストに枠線は不要
        fill(255); // 文字色を白に
        textSize(24); // 文字サイズを24ピクセルに
        const label = objects[i].label;
        const confidence = nf(objects[i].confidence * 100, 0, 2); // 確信度を%表示に
        
        // ラベルと確信度を描画
        text(`${label} ${confidence}%`, objects[i].x + 10, objects[i].y + 24);
      }
    }

    // 検出結果を受け取る関数
    function gotResult(error, results) {
      if (error) {
        console.error(error);
        return;
      }
      // 検出結果をグローバル変数に保存
      objects = results;
      console.log(objects);
    }
  </script>
</body>
</html>
```

### 実行結果の確認

このHTMLファイルをブラウザで開くと、道路の画像が表示され、検出された車（car）や人（person）が四角い枠で囲まれ、ラベルが表示されます。`gotResult` でコンソールに出力される `objects` 配列の中身を見ると、各物体の詳細な情報（x, y, width, heightなど）が確認できます。

---

## 練習問題

### 問題1

検出された物体のバウンディングボックス（四角い枠）の色を、現在の黒色から緑色に変更してみましょう。（ヒント: `draw` 関数の中の `stroke()` の引数を変更します）

### 問題2

現在は検出されたすべての物体を描画していますが、`'car'`（車）だけを検出して、そのバウンディングボックスを描画するようにコードを改造してみましょう。（ヒント: `draw` 関数内の `for` ループの中で、`if` 文を使って `objects[i].label` が `'car'` かどうかをチェックします）

---

## 解答

### 解答1

`draw` 関数内の `for` ループにある `stroke(0, 0, 0);` を `stroke(0, 255, 0);` に変更します。RGBの値で緑色を指定します。

```javascript
// ... draw関数の中 ...
for (let i = 0; i < objects.length; i++) {
  // 枠線の色を緑に設定
  stroke(0, 255, 0); 
  strokeWeight(4);
  noFill();
  rect(objects[i].x, objects[i].y, objects[i].width, objects[i].height);
  // ... 以下略 ...
}
```

### 解答2

`draw` 関数内の `for` ループに `if` 文を追加します。

```javascript
// ... draw関数の中 ...
for (let i = 0; i < objects.length; i++) {
  // もし検出した物体のラベルが 'car' だったら
  if (objects[i].label === 'car') {
    stroke(0, 0, 0);
    strokeWeight(4);
    noFill();
    rect(objects[i].x, objects[i].y, objects[i].width, objects[i].height);

    noStroke();
    fill(255);
    textSize(24);
    const label = objects[i].label;
    const confidence = nf(objects[i].confidence * 100, 0, 2);
    text(`${label} ${confidence}%`, objects[i].x + 10, objects[i].y + 24);
  }
}
```
