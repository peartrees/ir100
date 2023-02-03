# 9. シャーディングとレプリケーション

## 0. 準備

前章で作成したWebサーバ（以下、検索エンジンと呼ぶ）を異なるポートで2つ立ち上げよ。
それぞれ**シャード0, シャード1**と呼んで区別する。

## 1. 並列更新

以下のエンドポイントを持つ新たなWebサーバを実装せよ。

- `/update`: 製品群をHTTP POSTすると、各 `product_id` のMD5を計算する。その2の剰余が0の製品群をまとめてシャード0の、1の製品群をまとめてシャード1の、同名のエンドポイントに並列に転送する。

以下、このWebサーバを**ルータ**と呼ぶ。

## 2. 並列更新ベンチマーク

ルータを介して `8.5` を行え。

> ヒント：実行環境のリソースの枯渇に注意せよ。枯渇する場合は実装を見直し、それでも枯渇する場合は製品データを適度にサンプリングせよ（以降の問題でも同じ）。

## 3. 並列検索

ルータに新たに以下のエンドポイントを実装せよ。

- `/select`: `query` をHTTP GETすると、両シャードの同名のエンドポイントに並列に転送する。両レスポンスが揃ったら、製品群をまとめて並べ直し、優先度の上位10件までを返す。

## 4. 並列検索ベンチマーク

ルータを介して `8.6` を行え。

## 5. レスポンスの分割

ルータにおける `/select` の処理では半数までの製品群が捨てられる可能性がある。
捨てられる `product_title` の通信を省くため、以下のようにルータ-検索エンジン間でリクエスト/レスポンスを2往復させよ。

- 第1往復では、レスポンスは優先度と `product_id` のみを含む。これだけの情報があればルータで製品群の並べ直しを行えることに注意せよ。
- 第2往復では、レスポンスは並べ直しの結果上位の `product_title` のみを含む。

ただし、既存の機能とはエンドポイントを分けるか、リクエストパラメータによって初めて有効になるように実装せよ。
なお、検索エンジンへの追加実装も必要である。

## 6. レプリケーション

検索エンジンを異なるポートで4つ立ち上げよ。
それぞれ**シャード0レプリカ0, シャード0レプリカ1, シャード1レプリカ0, シャード1レプリカ1**と呼んで区別する。

ルータで `/update` の処理を行うとき、シャードごとの両レプリカに**同じ**製品群を並列に転送するようにし、再度ルータを介して `8.5` を行え。

## 7. 負荷分散

2レプリカの環境では、ルータで `/select` の処理を行うとき、（シャード0レプリカ0, シャード1レプリカ0）のペアまたは（シャード0レプリカ1, シャード1レプリカ1）のペアのうち空いている方を選んで転送することが考えられる。
これを実装し、再度ルータを介して `8.6` を行え。
ただしルータへのリクエストも並列に送る必要があることに注意せよ。

## 8. 動的レプリケーション

レプリカを動的に増やせるようにせよ。
ここで動的であるとは、既存の検索エンジンやルータを立ち上げ直したり、ルータから全ての製品を `/update` し直したりする必要が**ない**ことを指す。

## 9. 動的シャーディング

シャードを動的に増やせるようにせよ。

> ヒント：例えばMD5の2の剰余が0の製品群は、さらに4の剰余が0の製品群と2の製品群とに分割できる。