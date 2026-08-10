# watsonx Orchestrate × Confluent ハンズオンガイド

**Confluent Cloud (Apache Kafka) × watsonx Orchestrate イベント駆動型エージェント デモ手順書**

---

## このガイドについて

本ガイドは、IBM Developer Tutorial ["Building an event-driven agentic AI system with Apache Kafka on Confluent Cloud and watsonx Orchestrate"](https://developer.ibm.com/tutorials/event-driven-agentic-ai-system-confluent-watsonx-orchestrate/) をもとに作成した日本語デモ手順書です。

---

## ガイドの構成

| ページ | 内容 |
|---|---|
| [デモ概要](overview.md) | デモの全体像・訴求ポイント・アーキテクチャ |
| [前提条件](prerequisites.md) | 必要なアカウント・ツール・環境 |
| [セットアップ手順](steps/step1.md) | Step 1〜10 のセットアップ手順 |
| [デモシナリオ](demo-scenario.md) | 実演台本（4パターン） |
| [付録](appendix/troubleshooting.md) | トラブルシューティング・ファイル一覧・拡張アイデア |

---

## デモ概要（抜粋）

エージェントに「一問一答」ではなく **リアルタイムの業務イベントを継続的に読ませて判断させる** 構成のデモです。

1. POS / 入庫の在庫トランザクションが **Kafka** に流れ込む
2. **ksqlDB** がそれを集計して「現在の在庫状態」に変換する
3. **watsonx Orchestrate** のエージェントが MCP ツール経由でその状態を照会する
4. 在庫ゼロなら、別エージェントが**製品カタログ文書**を根拠に代替品を提案する

[→ デモ概要の詳細を読む](overview.md)

---

## 出典・サンプルコード

- **原典チュートリアル**: [IBM Developer](https://developer.ibm.com/tutorials/event-driven-agentic-ai-system-confluent-watsonx-orchestrate/)（Ahmed Azraq / Moises Dominguez Garcia、2026年3月17日）
- **サンプルコード**: [github.com/IBM/oic-i-agentic-ai-tutorials](https://github.com/IBM/oic-i-agentic-ai-tutorials) → `confluent-agents/`

!!! note "本書について"
    本書は原典チュートリアルの逐語訳ではなく、デモ実施用に再構成した日本語手順書です。
    コマンド・設定値・エージェント定義はリポジトリ実物から確認済みです。
    UI 操作手順および ksqlDB DDL は一部推定を含みます（[確認事項](appendix/extensions.md)参照）。
