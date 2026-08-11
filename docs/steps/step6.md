# ステップ6：スーパーバイザーエージェントを作成する

## 概要

このステップでは、**Store Associate Agent（店舗スタッフエージェント）**を作成します。このエージェントはスーパーバイザーエージェントとして機能し、これまでに作成した 2 つの専門エージェントを調整して、店舗スタッフ向けの単一のインタラクションポイントを提供します。

---

## Store Associate Agent の役割

このエージェントは Kafka やエンタープライズドキュメントと直接やり取りしません。代わりに、ユーザーのリクエストに基づいて専門エージェントにタスクを委任し、回答をまとめて顧客向けのわかりやすい回答にします。

### 担当する処理

- 店舗スタッフの質問を理解する
- 在庫確認を **SKU Availability Agent** に委任する
- 代替品推薦を（必要な場合）**Substitute Finder Agent** に委任する
- 最終的な簡潔な回答を顧客インタラクションに適した形で提示する

このパターンは、スーパーバイザーエージェントが複数のドメイン専門エージェントを調整する **watsonx Orchestrate のエージェントオーケストレーション**の動作を示しています。


## Store Associate Agent を作成する

エージェントの定義はリポジトリの YAML 設定ファイルとして提供されています。

### 手順

**1. YAML ファイルを確認する**

ステップ4でダウンロードした `Store_Associate_Agent.yaml` を使用します。

**2. Agent Development Kit でエージェントをインポートする**
Bobに以下の指示をしてください。
```bash
resources/Store_Associate_Agent.yamlの名称に"ご自身のイニシャル_"を付け加えて、
watsonx Orchestrateにインポートしてください
関連づけるサブエージェントの名称は先ほどインポートした
SKU_Availability_AgentとSubstitute_Finder_Agent
の名称に合わせてください。
```
---

## エージェントをテストする

エージェントが正しく動作するかを確認するために、2つのテストシナリオを実行します。

### テストA：在庫切れ + 代替品推薦

在庫切れのSKUについてエージェントに問い合わせてみます。
watsonx Orchestrateのエージェントに以下の質問をしてください。
```
Mall of EgyptでLAPTOP-DELL-XPS-15は入手できますか?
```

**期待される動作フロー：**

1. Store Associate Agent がリクエストを解析する
2. SKU Availability Agent に在庫確認を委任する
3. SKU Availability Agent が `get_sku_availability("LAPTOP-DELL-XPS-15", "MallOfEgypt")` を呼び出す
4. ksqlDB から `AVAILABLE_QUANTITY = 0` が返ってくる
5. Store Associate Agent が Substitute Finder Agent に代替品推薦を委任する
6. Substitute Finder Agent が製品カタログからセマンティック検索を行う
7. 最終的な回答が返ってくる

**期待される回答例：**<br>
![alt text](<step6_images/スクリーンショット 2026-08-11 19.09.01.png>)

### テストB：在庫あり

テストのためにStep4で実装したSKU Availability Agentに以下のような質問を投げかけて**在庫が1以上のSKU**をメモしておいてください。
```
Mall of Egyptで入手可能なSKUは何ですか?
```
![alt text](<step6_images/スクリーンショット 2026-08-11 18.15.23.png>)
```
Mall of Egyptで[先ほどメモしたSKU]は入手できますか?
```

**期待される動作フロー：**

1. Store Associate Agent がリクエストを解析する
2. SKU Availability Agent に在庫確認を委任する
3. ksqlDB から在庫数量が返ってくる（例：3）
4. Substitute Finder Agent への委任は行わない
5. 在庫数量を含む回答が返ってくる

**期待される回答例：**<br>
![alt text](<step6_images/スクリーンショット 2026-08-11 19.11.18.png>)

---

## ハンズオンのまとめ

このハンズオンでは、**IBM Bob**、**Confluent Cloud** と **watsonx Orchestrate** を使ってイベント駆動型エージェント AI システムを構築する方法を学びました。

### 構築したもの

| コンポーネント | 詳細 |
|--------------|------|
| Kafka トピック | `inventory.transactions`（ソース）、`inventory.availability`（派生） |
| ksqlDB テーブル | リアルタイムの在庫状況を継続的に計算 |
| MCP ツール | `get_sku_availability` — ksqlDB から在庫状況をクエリ |
| SKU_Availability_Agent | リアルタイムの在庫確認エージェント |
| Substitute_Finder_Agent | Agentic RAG による代替品推薦エージェント |
| Store_Associate_Agent | 2 つの専門エージェントを調整するスーパーバイザーエージェント |

### IBM Bob の貢献

IBM Bob は、自然言語の指示を動作するコード、ツール設定、検証済みのエージェント動作に変換することで、このハンズオンの各ステージを加速しました。低レベルのセットアップタスクではなく、アーキテクチャ、推論パターン、イベント駆動設計に集中できました。

---

## 発展的な拡張

このアーキテクチャは以下の方法で拡張できます：

### Apache Flink によるより高度なストリーム処理

Apache Flink を使って、Kafka 上でより高度なストリーム処理ロジックを実装できます：

- **時間ウィンドウの在庫トレンド分析**
- **異常検出**
- **外部参照データによるエンリッチメント**

これらの派生ストリームを、このハンズオンで使用した在庫状況と同じ方法でエージェントに公開できます。

### 追加のイベントソース

以下のような追加のリアルタイムシグナルを購読することで、エージェントを拡張できます：

- **プロモーション情報**（セール中の商品を考慮）
- **サプライヤーの遅延情報**（入荷予定日の告知）
- **需要予測**（季節的なトレンドの考慮）

---

## 参考リソース

- [watsonx Orchestrate の公開ハンズオン一覧](https://developer.ibm.com/components/watsonx-orchestrate/)
- [IBM Bob 無料トライアル](https://bob.ibm.com/trial/?utm_source=developer-content&cm_sp=ibmdev-_-developer-_-trial)
- [Confluent Cloud ドキュメント](https://docs.confluent.io/cloud/current/overview.html)
- [watsonx Orchestrate ADK ドキュメント](https://developer.ibm.com/tutorials/getting-started-with-watsonx-orchestrate/)
- [元のハンズオン（英語）](https://developer.ibm.com/tutorials/event-driven-agentic-ai-system-confluent-watsonx-orchestrate/)
