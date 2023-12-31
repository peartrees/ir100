# 6. 近似最近傍探索とベクトル類似検索

## 0. 準備

以下のエンコーダのexample useを実行できるように環境を整えよ。

> https://tfhub.dev/google/universal-sentence-encoder/4

## 1. ベクトル化

題材のエンコーダを実行して適当なクエリをベクトル化せよ。

## 2. ドット積

題材の製品ごとに、その `product_title` をベクトル化せよ。
同時に適当なクエリベクトルとの**ドット積**を計算し、それを優先度として上位10件の優先度と `product_title` を表示せよ。

ただし、この処理は非常に重いので、英語製品データを適度にサンプリングして行ってもよい。
その場合は、比較のため以降の問題でも同じサンプルを用いること。

## 3. ANNライブラリ

総当たりのベクトル演算は非常に重いので、**近似最近傍探索** (**ANN**) を行う。
適当なANNライブラリを入手し、その機能で `2.` を行え。
ただし多くのANNライブラリはドット積の降順に対応していないので、例えばコサイン類似度の降順またはユークリッド距離の昇順にしてみよ。

## 4. 代表点

続いて、転置インデックスとの相性が良いクラスタベースの近似最近傍探索を実装する。

まず、製品ベクトルをランダムにサンプリングし、それらにANNライブラリを利用してインデックスを張れ。
サンプル数は製品数の平方根とする。

以降、これらのサンプルを**代表点**と呼び、通し番号を振って区別する。

## 5. インデックス時の引き当て

製品ベクトルごとに代表点を1つ引き当て、その通し番号を付与せよ。

## 6. クエリ時の引き当て

適当なクエリベクトルで代表点を1つ引き当てよ。
続いて、そのクエリベクトルと代表点（の通し番号）を共有する製品ベクトル群についてのみドット積を計算し、それを優先度として上位10件の優先度と元の製品の `product_title` を表示せよ。

## 7. 近似の精度の評価

以上でクラスタベースの近似最近傍探索が可能になった。
いくつかのクエリについて `2.` と `6.` の結果を比較せよ。
また、`2.` の結果に含まれる製品のうち、`6.` の結果にも含まれるものの割合を表示せよ。

## 8. パラメータチューニング

ここで実装したクラスタベースの近似最近傍探索には、以下の通り多くのパラメータがあった。

- 用意する代表点の数
- 製品に対して引き当てる代表点の数
- クエリに対して引き当てる代表点の数

これらを何通りかチューニングし、その都度 `4.` から `7.` を行え。
ただし `6.` において評価値と実行時間のトレードオフがあると考えられるので、このとき実行時間も測定せよ。

## 9. クラスタリング

より適切な代表点の選び方もあると考えられる。
題材の製品をクラスタリングし、各クラスタの平均ベクトルを代表点として `5.` から `7.` を行え。
