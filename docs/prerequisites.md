# 前提条件

本デモを実施するには、以下の環境・ツールが必要です。

- **Confluent Cloud アカウント**（Kafka クラスター + ksqlDB クラスターを作成できること）
- **watsonx Orchestrate 環境**（SaaS もしくは Developer Edition）
- **watsonx Orchestrate ADK**（`orchestrate` CLI）がインストール済み・ログイン済み
- **Python 3.11 以上**
- **Git**
- （任意）**IBM Bob** — MCP ツール／エージェント定義の生成を効率化する場合

!!! tip "IBM Bob について"
    エージェント YAML の末尾には `# Made with Bob` のコメントがあり、原典では IBM Bob を使った開発フローも紹介されています。
