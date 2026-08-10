# ステップ2：ksqlDB で派生トピックを作成する

## 概要

このステップでは、inventory.availability という派生トピックを作成します。この派生トピックは、最初のトピックからのトランザクションを処理することで、各支店で各製品の在庫数を自動的に計算します。

そのために **ksqlDB クラスター**を作成します。ksqlDB はストリーム処理エンジンで、バックグラウンドで継続的に稼働し、SQL ライクなコマンドを使って Kafka トピックのデータを読み込み、計算し、結果を別のトピックに書き出します。

---

## Bob への指示例

Bob に以下のように指示してください：

```
inventory.transactions トピックには、以下のフィールドが含まれます。
「sku、branch、quantity、transaction_type」
トランザクションタイプは、追加在庫による正の数量の場合は Addition、
または POS からの販売トランザクションによる負の数量の場合は SALE のいずれかになります。

トランザクションを読み込み、inventory_availability テーブルを使用して在庫状況を計算する
ksqlDB クラスタをConfluent CLI を使用して作成してください。

このテーブルには、「sku、branch、available_quantity (数量の合計)」フィールドがあり、JSON 形式です。
```

Bob が Confluent CLI コマンドと ksqlDB の SQL クエリを生成・実行します。

---
## 補足事項

`.env`にログイン情報の追記をBobに求められることがあります。<br>
ただしGoogleアカウントなどでSSOログインしている場合はパスワードがわからないため、<br>
その場合はConfluent CLIを使ったログイン方法の手順をBobに聞いてください。

また、`CONFLUENT_CLOUD_API_KEY`などの追加の認証情報を求められることがあります。<br>
認証情報の取得方法はBobに聞けば答えてくれます。<br>
（Confluent Cloud UI右上のメニューから取得可能）<br>
![alt text](<step2_images/スクリーンショット 2026-08-11 1.12.26.png>)
---
## 確認

ksqlDBクラスターやテーブルが正常作成されたかどうか、Confluent Cloud UTで `Environments` > `default` > `cluster_0` > `ksqlDB` より確認ができます。
![alt text](<step2_images/スクリーンショット 2026-08-11 1.48.07.png>)

Bobに以下のように指示することでBobに確認をお願いすることもできます。（ただし時間がかかることがあります）
```
以下の項目を確認してください：

- ksqlDB クラスター のステータスが `Provisioned`または'Up' になっている
- `inventory_transactions` ストリームが正常に作成されている
- `inventory_availability` テーブルが正常に作成されている
- トピック一覧に `inventory.availability` が表示されている
```
---

## 次のステップ

ksqlDB の設定が完了したら、[ステップ3](step3.md) に進んでサンプルイベントをトピックに投入します。
