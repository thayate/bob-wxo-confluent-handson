# ステップ3：サンプルイベントをトピックに投入する

## 概要

この時点で、以下の 2 つのトピックが作成されています：

| トピック名 | 説明 |
|-----------|------|
| `inventory.transactions` | すべてのトランザクションを格納（プラスは在庫追加、マイナスは販売） |
| `inventory.availability` | 前のステップで作成した派生トピック（在庫状況を自動計算） |

現時点では `inventory.transactions` トピックは空の状態です。このステップではサンプルメッセージを投入します。

---

## Bob への指示例

Bob に以下のように指示してください：

```
Publish 20 sample messages to the topic inventory.transaction 
with 2 branches "MallOfEgypt and DubaiMall" and SKUs 3 laptop 
brands and 3 mobile brands, through a script. 
Make one of the laptop 0 quantities (all inventory consumed) 
in a branch. 
Then validate that the messages are correctly processed on ksqlDB.
```

Bob が Python スクリプトを生成してサンプルデータを投入し、ksqlDB での処理を検証します。

---

## ksqlDB の出力例

Bob による実行後、ksqlDB で以下のような結果を確認できます：

```
SKU                    | BRANCH      | AVAILABLE_QUANTITY
-----------------------|-------------|------------------
LAPTOP-DELL-XPS-15     | MallOfEgypt | 0
LAPTOP-HP-SPECTRE-X360 | MallOfEgypt | 5
LAPTOP-MACBOOK-PRO-16  | MallOfEgypt | 3
MOBILE-IPHONE-15       | DubaiMall   | 8
MOBILE-SAMSUNG-S24     | DubaiMall   | 12
LAPTOP-DELL-XPS-15     | DubaiMall   | 7
```

!!! info "データの見方"
    - `AVAILABLE_QUANTITY` が正の値 → 在庫あり
    - `AVAILABLE_QUANTITY` が 0 → 在庫切れ（後のステップで代替品推薦が動作します）
    - `AVAILABLE_QUANTITY` が負の値 → 在庫不足（異常系）

---

## 確認

以下の項目を確認してください：

- [ ] Confluent UI で `inventory.transactions` に 20 件のメッセージが表示されている
- [ ] ksqlDB で `SELECT * FROM INVENTORY_AVAILABILITY EMIT CHANGES;` が正常に実行できる
- [ ] 少なくとも 1 つの SKU で `AVAILABLE_QUANTITY` が 0 になっているレコードがある

---

## 次のステップ

サンプルデータの投入が完了したら、[ステップ4](step4.md) に進んで MCP ツールと AI エージェントを作成します。
