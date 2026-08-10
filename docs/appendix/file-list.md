# ファイル一覧（`confluent-agents/`）

| ファイル | 役割 |
|---|---|
| `.env.example` | 環境変数テンプレート |
| `requirements.txt` | Python 依存関係 |
| `sample-transactions.json` | サンプル在庫トランザクション（20件） |
| `setup_topic_with_samples.py` | トピック作成＋サンプル投入 |
| `produce_messages.py` | サンプル投入のみ |
| `clear_topic.py` | トピック削除＋再作成（デモリセット用） |
| `get_sku_availability.py` | MCP サーバー（ksqlDB 照会） |
| `test_mcp_client.py` | MCP サーバーのローカルテスト |
| `sku-availability-agent.yaml` | SKU Availability Agent 定義 |
| `Substitute_Finder_Agent.yaml` | Substitute Finder Agent 定義 |
| `Store_Associate_Agent.yaml` | スーパーバイザーエージェント定義 |
| `product-catalog.docx` | 代替品提案の根拠となる製品カタログ |
