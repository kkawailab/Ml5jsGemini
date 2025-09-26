
# 第5章: 独自のモデル作成 - 転移学習でAIを賢くする

おめでとうございます！いよいよ最終章です。この章では、これまでのチュートリアルで使ってきた「学習済みモデル」をさらに一歩進めて、あなただけの「オリジナル画像分類AI」を作成する方法を学びます。この技術を「転移学習」と呼びます。

## 1. 転移学習（Transfer Learning）とは？

転移学習とは、一言でいうと「賢いAIを先生として、新しいことを少しだけ追加で学習させる方法」です。

`MobileNet` のような大規模なモデルは、すでに何百万枚もの画像を見て、画像の中から「特徴」を見つけ出す方法を知っています。転移学習では、この「特徴を見つけ出す能力」を借りて、私たちが分類したい新しい少数の画像（例えば「自分の顔」と「他人の顔」）を学習させます。

これにより、ゼロからAIを学習させるのに比べて、はるかに少ないデータと時間で、自分専用のAIモデルを作ることができるのです。

## 2. `featureExtractor` でAIを再教育する

`ml5.js` で転移学習を行うには、`featureExtractor()` を使います。これは、`MobileNet` のような学習済みモデルから、画像の特徴を抽出する機能だけを取り出したものです。そして、その特徴を使って新しい分類器（`classifier`）をその場でトレーニングします。

手順は以下の通りです。

1.  `featureExtractor` と、それを利用する `classifier` を準備する。
2.  Webカメラを使い、「クラスA」「クラスB」のように分類したい対象の画像をAIに見せて、「これはクラスAだよ」と教える（`classifier.addImage()`）。
3.  集めた画像データを使って、AIに学習させる（`classifier.train()`）。
4.  学習が終わったら、Webカメラの映像を使ってリアルタイムで予測させる（`classifier.classify()`）。

## 3. サンプルコード

`index.html` を以下のコードに書き換えてください。このデモでは、「カテゴリA」と「カテゴリB」の2つの物体をその場でAIに学習させます。例えば、片方の手で「グー」を、もう片方で「パー」を見せて学習させることができます。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>第5章 | 転移学習</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.0/p5.js"></script>
  <script src="https://unpkg.com/ml5@latest/dist/ml5.min.js"></script>
</head>
<body>
  <h1>転移学習デモ</h1>
  <p>身の回りのものを2種類、AIに学習させてみよう！</p>
  <!-- UI要素 -->
  <button id="addA">カテゴリAの画像を追加</button>
  <button id="addB">カテゴリBの画像を追加</button>
  <button id="train">学習開始</button>
  <button id="predict">予測開始</button>
  <p>学習状況: <span id="loss"></span></p>
  <p>予測結果: <span id="result"></span></p>

  <script>
    let featureExtractor;
    let classifier;
    let video;
    let lossSpan;
    let resultSpan;

    function setup() {
      createCanvas(320, 240);
      video = createCapture(VIDEO);
      video.size(320, 240);
      video.hide();
      background(0);

      // UI要素を取得
      lossSpan = document.getElementById('loss');
      resultSpan = document.getElementById('result');

      // MobileNetから特徴抽出器を初期化
      featureExtractor = ml5.featureExtractor('MobileNet', modelReady);
      // 特徴抽出器を使って新しい分類器を作成
      classifier = featureExtractor.classification(video, videoReady);

      // ボタンのクリックイベントを設定
      setupButtons();
    }

    function draw() {
      image(video, 0, 0);
    }

    // モデルの準備ができたら呼ばれる
    function modelReady() {
      console.log('ベースモデル(MobileNet)読み込み完了！');
    }

    // ビデオの準備ができたら呼ばれる
    function videoReady() {
      console.log('ビデオ読み込み完了！');
    }

    // ボタンのセットアップ
    function setupButtons() {
      // カテゴリAの画像を追加
      document.getElementById('addA').addEventListener('click', () => {
        classifier.addImage('カテゴリA');
        console.log('カテゴリAの画像を追加しました');
      });

      // カテゴリBの画像を追加
      document.getElementById('addB').addEventListener('click', () => {
        classifier.addImage('カテゴリB');
        console.log('カテゴリBの画像を追加しました');
      });

      // 学習を開始
      document.getElementById('train').addEventListener('click', () => {
        console.log('学習を開始します...');
        classifier.train(whileTraining);
      });

      // 予測を開始
      document.getElementById('predict').addEventListener('click', () => {
        console.log('予測を開始します...');
        classifier.classify(gotResult);
      });
    }

    // 学習中の処理
    function whileTraining(loss) {
      if (loss == null) {
        console.log('学習完了！');
        lossSpan.innerText = '学習完了！';
        // 学習が終わったら、自動で予測を開始しても良い
        // classifier.classify(gotResult);
      } else {
        console.log('Loss:', loss);
        lossSpan.innerText = `学習中... (Loss: ${loss.toFixed(5)})`;
      }
    }

    // 予測結果を受け取る関数
    function gotResult(error, results) {
      if (error) {
        console.error(error);
        return;
      }
      resultSpan.innerText = `${results[0].label} (確信度: ${nf(results[0].confidence, 0, 2)})`;
      // 再度予測を実行してループ
      classifier.classify(gotResult);
    }
  </script>
</body>
</html>
```

### 使い方

1.  ブラウザでHTMLファイルを開き、カメラを許可します。
2.  Webカメラに「カテゴリA」として学習させたいもの（例：グーの手）を写し、「カテゴリAの画像を追加」ボタンを10〜20回クリックします。
3.  次に「カテゴリB」として学習させたいもの（例：パーの手）を写し、「カテゴリBの画像を追加」ボタンを同様にクリックします。
4.  「学習開始」ボタンをクリックします。学習状況のLoss（損失）の値が小さくなっていくのが確認できます。
5.  「学習完了！」と表示されたら、「予測開始」ボタンをクリックします。
6.  カメラにグーやパーをかざすと、AIがどちらであるかをリアルタイムで予測してくれるはずです！

---

## 練習問題

### 問題1

現在のコードを改造して、3つ目のカテゴリ「カテゴリC」を追加してみましょう。HTMLにボタンを追加し、JavaScriptを修正して、3種類の物体を分類できるようにしてください。

### 問題2

学習が完了したら、「予測開始」ボタンを押さなくても自動的に予測が開始されるようにコードを改造してみましょう。（ヒント: `whileTraining` 関数の中で、`loss` が `null` になった時が学習完了の合図です）

---

## 解答

### 解答1

**HTMLの変更箇所:**
`<body>` タグ内に「カテゴリC」用のボタンを追加します。
```html
<button id="addA">カテゴリAの画像を追加</button>
<button id="addB">カテゴリBの画像を追加</button>
<button id="addC">カテゴリCの画像を追加</button> <!-- この行を追加 -->
<button id="train">学習開始</button>
...
```

**JavaScript (`setupButtons`関数) の変更箇所:**
C用のボタンのイベントリスナーを追加します。
```javascript
function setupButtons() {
  // ... (AとBのボタンの設定は同じ) ...

  // カテゴリCの画像を追加
  document.getElementById('addC').addEventListener('click', () => {
    classifier.addImage('カテゴリC');
    console.log('カテゴリCの画像を追加しました');
  });

  // ... (trainとpredictのボタンの設定は同じ) ...
}
```

### 解答2

`whileTraining` 関数を以下のように変更します。`loss` が `null` になった（学習が完了した）タイミングで `classifier.classify(gotResult)` を呼び出します。

```javascript
function whileTraining(loss) {
  if (loss == null) {
    console.log('学習完了！');
    lossSpan.innerText = '学習完了！予測を開始します。';
    // 学習が完了したら、予測を開始する
    classifier.classify(gotResult);
  } else {
    console.log('Loss:', loss);
    lossSpan.innerText = `学習中... (Loss: ${loss.toFixed(5)})`;
  }
}
```
これで、「予測開始」ボタンが不要になります。
