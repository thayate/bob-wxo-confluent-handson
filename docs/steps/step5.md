# Step 5: ksqlDB クラスターの作成と集計定義

1. Confluent Cloud の対象 Environment で **ksqlDB クラスター**を作成
2. ksqlDB 用の **API Key / Secret** を作成
3. **Endpoint**（`https://pksqlc-xxxxx...:443`）を控え、`.env` の `KSQLDB_*` を更新

ksqlDB エディタで、トピックをストリームとしてマッピングし、SKU × 店舗で合計を取るテーブルを作成します。

```sql
-- 生イベントをストリームとしてマッピング
CREATE STREAM INVENTORY_TRANSACTIONS (
  sku              VARCHAR,
  branch           VARCHAR,
  quantity         INT,
  transaction_type VARCHAR,
  timestamp        VARCHAR,
  source           VARCHAR,
  reference        VARCHAR
) WITH (
  KAFKA_TOPIC  = 'inventory.transactions',
  VALUE_FORMAT = 'JSON'
);

-- 現在庫を導出したマテリアライズドビュー（テーブル）
CREATE TABLE INVENTORY_AVAILABILITY
WITH (KAFKA_TOPIC = 'inventory.availability') AS
SELECT
  sku,
  branch,
  SUM(quantity) AS available_quantity
FROM INVENTORY_TRANSACTIONS
GROUP BY sku, branch
EMIT CHANGES;
```

!!! warning "要確認"
    上記 DDL は MCP ツールのクエリと原典が言及する導出トピック名から再構成したものです。**列順・列名を含む正確な DDL は原典チュートリアルの該当ステップを参照してください。**

動作確認（ksqlDB エディタ）:

```sql
SELECT * FROM INVENTORY_AVAILABILITY;
SELECT * FROM INVENTORY_AVAILABILITY WHERE SKU='LAPTOP-DELL-XPS-15' AND BRANCH='DubaiMall';
```
