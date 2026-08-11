# イベント駆動型エージェントAIシステム構築ハンズオン

Confluent Cloud（Apache Kafka）と watsonx Orchestrate を使ったイベント駆動型エージェントAIシステムの日本語ハンズオンガイドです。

## 概要

IBM Bob、Confluent Cloud のマネージド Apache Kafka サービス、watsonx Orchestrate を組み合わせて、リアルタイムの在庫確認と代替品推薦を行うエージェントシステムを構築します。

元チュートリアル：<br>
[Building an event-driven agentic AI system with Apache Kafka on Confluent Cloud and watsonx Orchestrate](https://developer.ibm.com/tutorials/event-driven-agentic-ai-system-confluent-watsonx-orchestrate/)

## ドキュメントの参照
以下のリンクからガイドにアクセスできます。<br>
[https://thayate.github.io/bob-wxo-confluent-handson/](https://thayate.github.io/bob-wxo-confluent-handson/)

## ハンズオンの構成

| ページ | 内容 |
|--------|------|
| 前提条件 | 必要なアカウント・環境のセットアップ |
| アーキテクチャ概要 | システム全体の構成とデータフロー |
| ステップ1 | Confluent Cloud に Kafka トピックを作成する |
| ステップ2 | ksqlDB で派生トピックを作成する |
| ステップ3 | サンプルイベントをトピックに投入する |
| ステップ4 | MCP ツールと AI エージェントを watsonx Orchestrate に作成する |
| ステップ5 | Agentic RAG エージェントを作成する |
| ステップ6 | スーパーバイザーエージェントを作成・テストする |