# デモシナリオ（実演台本）

`Store_Associate_Agent` のチャットで以下を順に実行します。

| # | 入力 | 期待される挙動 | 見せどころ |
|---|---|---|---|
| 1 | `Do you have LAPTOP-DELL-XPS-15?` | 店舗名を聞き返す | 必須情報の確認ルールが効いている |
| 2 | `Mall of Egypt` / `Do you have LAPTOP-HP-SPECTRE-X360 in Dubai Mall?` | 「在庫あり（52台）」と回答 | Kafka → ksqlDB → MCP のリアルタイム経路 |
| 3 | `Do you have LAPTOP-DELL-XPS-15 in Dubai Mall?` | 在庫 0 を検出 → 代替品を最大 3 件提案 | マルチエージェント連携 + 文書グラウンディング |
| 4 | （別ターミナルで追加トランザクションを投入し）再度同じ質問 | 回答が新しい在庫状態に変わる | **イベント駆動でエージェントの結論が更新される** |

## #4 の準備

`sample-transactions.json` に `LAPTOP-DELL-XPS-15` / `DubaiMall` の入庫レコードを追記し、`produce_messages.py` を再実行すると在庫が復活します（ksqlDB の集計に反映されるまで数秒待つ）。

## #3 の追い打ち質問（提案の根拠を見せる）

- 「Why did you recommend that model?」→ カタログ上の共通属性を根拠に説明する

---

!!! tip "店舗名の正規化について"
    エージェントの instructions では `DubaiMall` / `MallOfEgypt`（スペースなし）で定義されています。ユーザー入力の「Dubai Mall」「Mall of Egypt」を正規化できるかは LLM 依存のため、デモ前に必ず実機確認してください。
