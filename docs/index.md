# イベント駆動型エージェント構築ハンズオン

> **元ドキュメント:** [Building an event-driven agentic AI system with Apache Kafka on Confluent Cloud and watsonx Orchestrate](https://developer.ibm.com/tutorials/event-driven-agentic-ai-system-confluent-watsonx-orchestrate/)

## このハンズオンについて

このハンズオンでは、**IBM Bob**、**Confluent CloudのManaged Apache Kafkaサービス**と**watsonx Orchestrate**を使って、イベント駆動型エージェントシステムを構築する方法を解説します。

エージェントはKafkaトピックのイベントをリアルタイムで消費・分析し、ビジネスドキュメント（製品カタログ等）と照合することで、ライブの業務シグナルを解釈・説明します。このパターンは以下のようなユースケースに特に有効です：

- 業務上の問題をリアルタイムに監視する
- 新たなリスクを早期検知する
- 複雑なシステムにまたがる変更をサマリーする

---

## 何を作るか

本ハンズオンでは、**小売業向けAIエージェント**を watsonx Orchestrate 上に構築します。このエージェントは以下の処理を行います：

1. Confluent Kafka と連携し、指定ブランチ（店舗）の特定商品の**在庫状況をリアルタイムで確認**する
2. 指定ブランチの在庫が0の場合、製品説明エンタープライズドキュメントを参照した **Agentic RAG によって類似代替品を推薦**する

### シナリオ

クリスマス休暇のような繁忙期には、人気商品はあっという間に在庫切れになり、店舗間で在庫状況が刻一刻と変わります。店舗スタッフが顧客から次のような問い合わせを受けた場面を想定します：

> 「Mall of Egypt に Dell XPS ラップトップはありますか？なければ、代わりになるラップトップはありますか？」

ハンズオン完了後、エージェントアーキテクチャにおけるイベントストリーミングの役割と、watsonx Orchestrate がリアルタイムデータを推論に活用する方法を深く理解できます。

---

## IBM Bob の役割

[IBM Bob](https://bob.ibm.com/trial/?utm_source=developer-content&cm_sp=ibmdev-_-developer-_-trial) は、AIソフトウェア開発パートナーとして、以下の作業を加速します：

- Confluent Cloud での Kafka トピックおよび ksqlDB クラスターの作成
- トピックへのサンプルイベントの投入
- watsonx Orchestrate 上での MCP ツールおよび AI エージェントの構築
- Agentic RAG によるエンタープライズドキュメントでの Kafka イベント強化

---

## ハンズオンの流れ

| ステップ | 内容 |
|----------|------|
| [前提条件](prerequisites.md) | 必要なアカウント・環境のセットアップ |
| [アーキテクチャ概要](overview.md) | システム全体の構成とデータフロー |
| [ステップ1](steps/step1.md) | Confluent Cloud に Kafka トピックを作成する |
| [ステップ2](steps/step2.md) | ksqlDB で派生トピックを作成する |
| [ステップ3](steps/step3.md) | サンプルイベントをトピックに投入する |
| [ステップ4](steps/step4.md) | MCP ツールと AI エージェントを watsonx Orchestrate に作成する |
| [ステップ5](steps/step5.md) | Agentic RAG エージェントを作成する |
| [ステップ6](steps/step6.md) | スーパーバイザーエージェントを作成・テストする |

---

!!! info "対象読者"
    このハンズオンは、Apache Kafka の基本概念（トピック、コンシューマー等）の知識を持ち、AI エージェントの実装に興味がある開発者・アーキテクトを対象としています。

!!! note "バージョン情報"
    このハンズオンは watsonx Orchestrate ADK バージョン **2.1** で動作検証済みです。
