# 5. Learning to Rank

## 0. 準備

英語結合データ中の各エントリは `split` が `train` であるものと `test` であるものに分けられる。
これらを別のデータに分けよ。
前者を**訓練データ**、後者を**テストデータ**とする。

## 1. 特徴量

英語結合データ中の各エントリ（クエリと製品のペア）について、それぞれ10個を超える特徴量を抽出せよ。
例えば以下の20個が考えられる。

- `query` と {`product_title`, `product_description`} について計算した {TF, TFIDF, BM25} の、`query` 中の単語間の｛総和、平均、分散｝。
- {`product_title`, `product_description`} のフィールド長。

## 2. 前処理

前問で得られた特徴量について、一般的な正規化を行え。

## 3. 線形モデル

ロジスティック回帰と**訓練データ**を用いて、前問で得られた特徴量を入力すると、エントリ中のクエリと製品が適合であるか不適であるかを分類するモデルを訓練せよ。
ただし `esci_label` が `I` **ではない**エントリを適合とする。

## 4. ランキングモデルの評価

前問で訓練したモデルと**テストデータ**を用いて、`query_id` ごとにモデルの出力の順にエントリをランキングせよ。
ただし、適合が上位、不適が下位とする。

`3.4`, `3.5` を参照して、nDCG@10の平均値を示せ。
以下、モデルを訓練したときは同様に評価を行え。

## 5. 決定木

XGBoostと訓練データを用いて、`2.` で得られた特徴量を入力すると優先度を出力するモデルを訓練せよ。
ただし、真の優先度は `esci_label` が `E`, `S`, `C`, `I` の順で高いものとし、絶対値はドントケアとする（例えばペアワイズ学習を行う）。

> ヒント：いわゆるlearning to rankを行いたいので、エントリを `query_id` でグループ化し、目的関数に `rank` を指定せよ。

## 6. バリデーションデータと学習率

訓練データから適当な比率でバリデーションデータを取り分けよ。

前問においてXGBoostにバリデーションデータも入力し、バリデーションデータを用いた評価が上がりきったところで、ちょうど訓練が終了するように学習率をチューニングせよ。

## 7. 特徴選択

`5.` または `6.` の問題設定において、XGBoostの機能でfeature importanceを確認せよ。
また、feature importanceの上位10個の特徴量のみを用いて同じ問題を解け。

## 8. BERT

ロジスティック回帰の代わりにBERTを用いて `3.` を行え。
ただし入力は特徴量ではなく、`query` と `product_title` そのものを、スペースでトークンに区切って与えよ。

## 9. Siamese BERT

`query` を受け取ってから `query` と全ての `product_title` をBERTに入力すると、検索エンジンとしては実用的な時間で動作しないと考えられる。
そこで、siamese BERTを用いて前問を解け。
