# ステップ2：ksqlDB で派生トピックを作成する

## 概要

このステップでは、`inventory.transactions` トピックのトランザクションを処理し、**ブランチ・SKU ごとの現在の在庫数量を自動計算する**派生トピック `inventory.availability` を作成します。

そのために **ksqlDB クラスター**を作成します。ksqlDB はストリーム処理エンジンで、バックグラウンドで継続的に稼働し、SQL ライクなコマンドを使って Kafka トピックのデータを読み込み、計算し、結果を別のトピックに書き出します。

---

## Bob への指示例

Bob に以下のように指示してください：

```
The inventory.transactions topic includes the following fields 
"sku, branch, quantity, transaction_type". 
Transaction Type can be either Addition for positive quantity 
through additional inventory or SALE for negative quantity 
through sales transaction from pos. 
Create a ksqlDB cluster through Confluent CLI, then read the 
transactions and calculates the availability through 
inventory_availability table with "sku, branch, and 
available_quantity (sum of the quantities)" fields and JSON format.
```

Bob が Confluent CLI コマンドと ksqlDB の SQL クエリを生成・実行します。

---

## 確認

以下の項目を確認してください：

- [ ] ksqlDB クラスター `sku-availability-calculator` のステータスが `Provisioned` になっている
- [ ] `inventory_transactions` ストリームが正常に作成されている
- [ ] `inventory_availability` テーブルが正常に作成されている
- [ ] Confluent UI のトピック一覧に `inventory.availability` が表示されている

---

## 次のステップ

ksqlDB の設定が完了したら、[ステップ3](step3.md) に進んでサンプルイベントをトピックに投入します。
