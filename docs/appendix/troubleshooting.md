# トラブルシューティング

| 症状 | 確認ポイント |
|---|---|
| `Missing required environment variables` | `.env` の必須項目（`BOOTSTRAP_SERVERS` / `KAFKA_API_KEY` / `KAFKA_API_SECRET`）を確認 |
| `Please update .env file with your actual ... credentials` | テンプレートの `xxxxx` や `your-kafka-api-key` が残っている |
| MCP ツールが空結果を返す | ksqlDB のテーブル名が `INVENTORY_AVAILABILITY` か、トピックにデータが入っているかを確認 |
| ksqlDB 認証エラー | ksqlDB 用 API Key は Kafka クラスター用とは**別**。混同していないか確認 |
| Orchestrate でツールが見つからない | `--package-root` が絶対パスになっているか確認 |
| 在庫が更新されない | ksqlDB の集計反映待ち。数秒後に再照会 |
