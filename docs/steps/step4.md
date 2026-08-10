# Step 4: トピック作成とサンプルデータ投入

サンプルデータ `sample-transactions.json` は **6 SKU × 2 店舗、計 20 件**の在庫トランザクションです。

- `quantity` が正 → 入庫（`ADDITION`、仕入先からの入荷）
- `quantity` が負 → 販売（POS での顧客購入）

レコード例:

```json
{"sku": "LAPTOP-DELL-XPS-15", "branch": "DubaiMall", "quantity": 50,
 "transaction_type": "ADDITION", "timestamp": "2025-12-29T08:00:00Z",
 "source": "inventory_manager", "reference": "PO-2025-001"}
```

トピック作成 + データ投入（無限リテンションでトピックを作成し、サンプルを流し込む）:

```bash
python3 setup_topic_with_samples.py
# トピック名を指定する場合
python3 setup_topic_with_samples.py my.custom.topic
```

すでにトピックがある場合、メッセージ投入のみ実行するには:

```bash
python3 produce_messages.py
```

デモをやり直したい（トピックを空に戻したい）場合:

```bash
python3 clear_topic.py    # トピックを削除して再作成
```

Confluent Cloud の **Topics → inventory.transactions → Messages** で 20 件が入っていることを確認します。

## 投入後の在庫集計結果（デモの期待値）

| 店舗 | SKU | 在庫数 |
|---|---|---|
| DubaiMall | **LAPTOP-DELL-XPS-15** | **0** ← 代替品提案のトリガー |
| DubaiMall | LAPTOP-HP-SPECTRE-X360 | 52 |
| DubaiMall | MOBILE-SAMSUNG-S24-ULTRA | 60 |
| MallOfEgypt | LAPTOP-MACBOOK-PRO-16 | 37 |
| MallOfEgypt | MOBILE-GOOGLE-PIXEL-8-PRO | 53 |
| MallOfEgypt | MOBILE-IPHONE-17-PRO-MAX | 97 |
