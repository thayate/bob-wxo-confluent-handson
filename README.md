# watsonx Orchestrate × Confluent ハンズオンガイド

Confluent Cloud (Apache Kafka) × watsonx Orchestrate イベント駆動型エージェント デモの手順書です。

## ドキュメントサイト

**GitHub Pages**: https://thayate.github.io/bob-wxo-confluent-handson/

## 概要

エージェントに「一問一答」ではなく **リアルタイムの業務イベントを継続的に読ませて判断させる** 構成のデモです。

小売の店舗スタッフ向けアシスタントを題材に、

1. POS / 入庫の在庫トランザクションが Kafka に流れ込む
2. ksqlDB がそれを集計して「現在の在庫状態」に変換する
3. watsonx Orchestrate のエージェントが MCP ツール経由でその状態を照会する
4. 在庫ゼロなら、別エージェントが**製品カタログ文書**を根拠に代替品を提案する

## 出典

IBM Developer Tutorial "Building an event-driven agentic AI system with Apache Kafka on Confluent Cloud and watsonx Orchestrate"  
https://developer.ibm.com/tutorials/event-driven-agentic-ai-system-confluent-watsonx-orchestrate/

サンプルコード: https://github.com/IBM/oic-i-agentic-ai-tutorials → `confluent-agents/`
