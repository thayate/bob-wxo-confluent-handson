# 拡張アイデア

追加の Kafka トピックを購読させることで、プロモーション情報、サプライヤー遅延、需要予測などの別のリアルタイムシグナルと在庫データを突き合わせた推論に拡張できます。

---

## 確認事項（原典で要確認）

本書作成時、原典ページはクライアントサイドレンダリングのため本文を機械取得できませんでした。以下は **GitHub リポジトリの実物から確認済みの情報 + 推定**で構成しています。デモ実施前に原典で照合してください。

### 確認済み（リポジトリ実物ベース）

- 全 Python スクリプトの挙動、環境変数名、ツール仕様
- 3 つのエージェント YAML の定義内容
- サンプルデータの内容と集計結果
- `orchestrate toolkits add` コマンド（原典本文の該当箇所も取得済み）

### 推定・要確認

- ksqlDB の `CREATE STREAM` / `CREATE TABLE` の正確な DDL（Step 5）
- Confluent Cloud / watsonx Orchestrate の UI 操作の細部
- Substitute Finder Agent のナレッジ登録手順（UI 操作 or CLI）
- `orchestrate agents import` の正確なオプション（ADK バージョン依存）
- 原典で紹介されている IBM Bob を用いた開発フローの詳細  
  （関連チュートリアル: "Using IBM Bob to build watsonx Orchestrate agents and MCP tools"）
