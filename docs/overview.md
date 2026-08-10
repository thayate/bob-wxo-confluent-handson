# デモ概要

## 何を見せるデモか

エージェントに「一問一答」ではなく **リアルタイムの業務イベントを継続的に読ませて判断させる** 構成のデモ。

小売の店舗スタッフ向けアシスタントを題材に、

1. POS / 入庫の在庫トランザクションが Kafka に流れ込む
2. ksqlDB がそれを集計して「現在の在庫状態」に変換する
3. watsonx Orchestrate のエージェントが MCP ツール経由でその状態を照会する
4. 在庫ゼロなら、別エージェントが**製品カタログ文書**を根拠に代替品を提案する

という流れを実演します。

## 訴求ポイント（お客様説明用）

| 論点 | 説明 |
|---|---|
| イベント駆動 | ポーリングや静的な入力ではなく、ストリームから導出された最新状態を参照 |
| 責務の分離 | 「イベント層」「導出状態層」「エージェント判断層」が分かれており、監査・ガバナンスがしやすい |
| 生データを直接読ませない | 生イベントではなく集計済みステート（テーブル）を参照させることで、判断がノイズに引きずられない |
| マルチエージェント | スーパーバイザー型（Store Associate Agent）が 2 つの専門エージェントを使い分け |
| 文書グラウンディング | 代替品提案は製品カタログ文書に限定（ハルシネーション抑止をプロンプトで明示） |

## アーキテクチャ

```
[サンプル在庫トランザクション]
        │  produce_messages.py / setup_topic_with_samples.py
        ▼
 Confluent Cloud : Kafka Topic  (inventory.transactions)
        │  ksqlDB : STREAM → TABLE 集計
        ▼
 INVENTORY_AVAILABILITY (現在庫テーブル / inventory.availability)
        │  ksqlDB REST API (/query-stream)
        ▼
 MCP サーバー get_sku_availability.py  (FastMCP)
        │  orchestrate toolkits add --kind mcp
        ▼
 watsonx Orchestrate
   ├─ SKU_Availability_Agent   … 在庫照会（MCPツール利用）
   ├─ Substitute_Finder_Agent  … 代替品提案（product-catalog.docx をナレッジ参照）
   └─ Store_Associate_Agent    … スーパーバイザー（上記2つを統括）
```
