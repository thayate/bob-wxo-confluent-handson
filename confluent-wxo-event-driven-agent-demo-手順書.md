# Confluent Cloud (Apache Kafka) × watsonx Orchestrate イベント駆動型エージェント デモ手順書

**出典（原典）**: IBM Developer Tutorial "Building an event-driven agentic AI system with Apache Kafka on Confluent Cloud and watsonx Orchestrate"（Ahmed Azraq / Moises Dominguez Garcia、2026年3月17日）
<https://developer.ibm.com/tutorials/event-driven-agentic-ai-system-confluent-watsonx-orchestrate/>

**サンプルコード**: <https://github.com/IBM/oic-i-agentic-ai-tutorials> → `confluent-agents/`

> 本書は原典チュートリアルの逐語訳ではなく、デモ実施用に再構成した日本語手順書です。
> コマンド・設定値・エージェント定義はリポジトリ実物から確認済み。UI 操作手順および ksqlDB DDL は一部推定を含みます（末尾「確認事項」参照）。

---

## 1. デモ概要

### 何を見せるデモか

エージェントに「一問一答」ではなく **リアルタイムの業務イベントを継続的に読ませて判断させる** 構成のデモ。

小売の店舗スタッフ向けアシスタントを題材に、

1. POS / 入庫の在庫トランザクションが Kafka に流れ込む
2. ksqlDB がそれを集計して「現在の在庫状態」に変換する
3. watsonx Orchestrate のエージェントが MCP ツール経由でその状態を照会する
4. 在庫ゼロなら、別エージェントが**製品カタログ文書**を根拠に代替品を提案する

という流れを実演します。

### 訴求ポイント（お客様説明用）

| 論点 | 説明 |
|---|---|
| イベント駆動 | ポーリングや静的な入力ではなく、ストリームから導出された最新状態を参照 |
| 責務の分離 | 「イベント層」「導出状態層」「エージェント判断層」が分かれており、監査・ガバナンスがしやすい |
| 生データを直接読ませない | 生イベントではなく集計済みステート（テーブル）を参照させることで、判断がノイズに引きずられない |
| マルチエージェント | スーパーバイザー型（Store Associate Agent）が 2 つの専門エージェントを使い分け |
| 文書グラウンディング | 代替品提案は製品カタログ文書に限定（ハルシネーション抑止をプロンプトで明示） |

### アーキテクチャ

```
[サンプル在庫トランザクション]
        │  produce_messages.py / setup_topic_with_samples.py
        ▼
 Confluent Cloud : Kafka Topic  (inventory.transactions)
        │  ksqlDB : STREAM → TABLE 集計
        ▼
 INVENTORY_AVAILABILITY (現在庫テーブル / inventory.availability)
        │  ksqlDB REST API (/query-stream)
        ▼
 MCP サーバー get_sku_availability.py  (FastMCP)
        │  orchestrate toolkits add --kind mcp
        ▼
 watsonx Orchestrate
   ├─ SKU_Availability_Agent   … 在庫照会（MCPツール利用）
   ├─ Substitute_Finder_Agent  … 代替品提案（product-catalog.docx をナレッジ参照）
   └─ Store_Associate_Agent    … スーパーバイザー（上記2つを統括）
```

---

## 2. 前提条件

- Confluent Cloud アカウント（Kafka クラスター + ksqlDB クラスターを作成できること）
- watsonx Orchestrate 環境（SaaS もしくは Developer Edition）
- watsonx Orchestrate ADK（`orchestrate` CLI）がインストール済み・ログイン済み
- Python 3.11 以上
- Git
- （任意）IBM Bob — MCP ツール／エージェント定義の生成を効率化する場合

> 参考: エージェント YAML の末尾には `# Made with Bob` のコメントがあり、原典では IBM Bob を使った開発フローも紹介されています。

---

## 3. 手順

### Step 1: リポジトリの取得と環境準備

```bash
git clone https://github.com/IBM/oic-i-agentic-ai-tutorials
cd oic-i-agentic-ai-tutorials/confluent-agents

python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

`requirements.txt` の内容:

```
confluent-kafka==2.14.0
python-dotenv==1.2.2
requests==2.32.5
fastmcp==3.1.1
```

---

### Step 2: Confluent Cloud で Kafka クラスターと API キーを作成

1. Confluent Cloud にログインし、Environment を作成（または既存のものを選択）
2. Kafka クラスター（Basic で可）を作成
3. **Cluster settings → Endpoints** から **Bootstrap server** を控える
   （形式: `pkc-xxxxx.<region>.<provider>.confluent.cloud:9092`）
4. **API Keys → Add key**（クラスター用）を作成し、**Key / Secret** を控える

---

### Step 3: `.env` ファイルの作成

`.env.example` をコピーして `.env` を作成し、値を埋めます。

```bash
cp .env.example .env
```

```dotenv
# Kafka クラスター
BOOTSTRAP_SERVERS=pkc-xxxxx.region.provider.confluent.cloud:9092
KAFKA_API_KEY=<Kafka API Key>
KAFKA_API_SECRET=<Kafka API Secret>

# トピック
TOPIC_NAME=inventory.transactions

# ksqlDB（Step 5 で取得した値を後から追記）
KSQLDB_ENDPOINT=https://pksqlc-xxxxx.region.provider.confluent.cloud:443
KSQLDB_API_KEY=<ksqlDB API Key>
KSQLDB_API_SECRET=<ksqlDB API Secret>
```

> **注意**: `.env` は `.gitignore` 済み。デモ用資材としても Git にコミットしないこと。

---

### Step 4: トピック作成とサンプルデータ投入

サンプルデータ `sample-transactions.json` は **6 SKU × 2 店舗、計 20 件**の在庫トランザクションです。

- `quantity` が正 → 入庫（`ADDITION`、仕入先からの入荷）
- `quantity` が負 → 販売（POS での顧客購入）

レコード例:

```json
{"sku": "LAPTOP-DELL-XPS-15", "branch": "DubaiMall", "quantity": 50,
 "transaction_type": "ADDITION", "timestamp": "2025-12-29T08:00:00Z",
 "source": "inventory_manager", "reference": "PO-2025-001"}
```

トピック作成 + データ投入（無限リテンションでトピックを作成し、サンプルを流し込む）:

```bash
python3 setup_topic_with_samples.py
# トピック名を指定する場合
python3 setup_topic_with_samples.py my.custom.topic
```

すでにトピックがある場合、メッセージ投入のみ実行するには:

```bash
python3 produce_messages.py
```

デモをやり直したい（トピックを空に戻したい）場合:

```bash
python3 clear_topic.py    # トピックを削除して再作成
```

Confluent Cloud の **Topics → inventory.transactions → Messages** で 20 件が入っていることを確認します。

#### 投入後の在庫集計結果（デモの期待値）

| 店舗 | SKU | 在庫数 |
|---|---|---|
| DubaiMall | **LAPTOP-DELL-XPS-15** | **0** ← 代替品提案のトリガー |
| DubaiMall | LAPTOP-HP-SPECTRE-X360 | 52 |
| DubaiMall | MOBILE-SAMSUNG-S24-ULTRA | 60 |
| MallOfEgypt | LAPTOP-MACBOOK-PRO-16 | 37 |
| MallOfEgypt | MOBILE-GOOGLE-PIXEL-8-PRO | 53 |
| MallOfEgypt | MOBILE-IPHONE-17-PRO-MAX | 97 |

---

### Step 5: ksqlDB クラスターの作成と集計定義

1. Confluent Cloud の対象 Environment で **ksqlDB クラスター**を作成
2. ksqlDB 用の **API Key / Secret** を作成
3. **Endpoint**（`https://pksqlc-xxxxx...:443`）を控え、`.env` の `KSQLDB_*` を更新

ksqlDB エディタで、トピックをストリームとしてマッピングし、SKU × 店舗で合計を取るテーブルを作成します。

```sql
-- 生イベントをストリームとしてマッピング
CREATE STREAM INVENTORY_TRANSACTIONS (
  sku              VARCHAR,
  branch           VARCHAR,
  quantity         INT,
  transaction_type VARCHAR,
  timestamp        VARCHAR,
  source           VARCHAR,
  reference        VARCHAR
) WITH (
  KAFKA_TOPIC  = 'inventory.transactions',
  VALUE_FORMAT = 'JSON'
);

-- 現在庫を導出したマテリアライズドビュー（テーブル）
CREATE TABLE INVENTORY_AVAILABILITY
WITH (KAFKA_TOPIC = 'inventory.availability') AS
SELECT
  sku,
  branch,
  SUM(quantity) AS available_quantity
FROM INVENTORY_TRANSACTIONS
GROUP BY sku, branch
EMIT CHANGES;
```

> ⚠️ **要確認**: 上記 DDL は MCP ツールのクエリ（`SELECT * FROM INVENTORY_AVAILABILITY;` が `sku / branch / available_quantity` の 3 列を返す）と、原典が言及する導出トピック名 `inventory.availability` から再構成したものです。**列順・列名を含む正確な DDL は原典チュートリアルの該当ステップを参照してください。**

動作確認（ksqlDB エディタ）:

```sql
SELECT * FROM INVENTORY_AVAILABILITY;
SELECT * FROM INVENTORY_AVAILABILITY WHERE SKU='LAPTOP-DELL-XPS-15' AND BRANCH='DubaiMall';
```

---

### Step 6: MCP ツールのローカル検証

`get_sku_availability.py` は FastMCP ベースの MCP サーバーです。

- ツール名: `get_sku_availability`
- 引数: `sku`（任意）, `branch`（任意）— 空文字ならフィルタなし
- 動作: ksqlDB の `/query-stream` エンドポイントに Basic 認証で POST し、`INVENTORY_AVAILABILITY` を検索
- 戻り値: `{"results": [{"sku": ..., "branch": ..., "available_quantity": ...}]}` の JSON 文字列

ローカルテスト:

```bash
python3 test_mcp_client.py
```

テストクライアントは以下 4 パターンを実行します。

1. 全件取得
2. SKU 指定（`LAPTOP-DELL-XPS-15`）
3. 店舗指定（`DubaiMall`）
4. SKU + 店舗指定

---

### Step 7: MCP ツールを watsonx Orchestrate に取り込む

`--package-root` は**プロジェクトの絶対パス**に置き換えてください。

```bash
orchestrate toolkits add \
  --kind mcp \
  --name "sku-availability-checker" \
  --description "Real-time inventory availability checker using Confluent Kafka and ksqlDB" \
  --language python \
  --package-root "/absolute/path/to/oic-i-agentic-ai-tutorials/confluent-agents" \
  --command "python3 get_sku_availability.py" \
  --tools "*"
```

---

### Step 8: SKU Availability Agent のインポート

```bash
orchestrate agents import -f sku-availability-agent.yaml
```

定義の要点（`sku-availability-agent.yaml`）:

- `name`: `SKU_Availability_Agent`
- `llm`: `groq/openai/gpt-oss-120b`
- `tools`: `sku-availability-checker:get_sku_availability`
- instructions: 在庫照会の手順、在庫 0 の明示、対象 SKU・店舗の一覧を明記

**確認**: watsonx Orchestrate UI → **Manage agents** → `SKU_Availability_Agent` を開き、MCP ツールが紐づいていることを確認してテスト実行。

テスト例:
- 「Check availability of LAPTOP-DELL-XPS-15 in DubaiMall」
- 「Show all inventory in MallOfEgypt」

---

### Step 9: Substitute Finder Agent の作成

在庫がない SKU に対して、**製品カタログ文書に基づいて**代替品を提案する専門エージェントです。

1. watsonx Orchestrate でナレッジ（Knowledge base）を作成し、`product-catalog.docx` をアップロード
2. エージェントをインポート

```bash
orchestrate agents import -f Substitute_Finder_Agent.yaml
```

定義の要点（`Substitute_Finder_Agent.yaml`）:

- `knowledge`: `enterprise_documents` / `semantic_similarity` / `top_k: 5`
- 挙動ルール:
  - 必ず製品カタログ文書を根拠にする
  - 文書外の一般知識に依存しない／製品や仕様を捏造しない
  - 抽出する属性: カテゴリ、ブランド、フォームファクター、画面サイズ、プロセッサクラス、メモリ、ストレージ、用途、主要機能
  - 類似度でランキングし、**最大 3 件**を提案
  - 見つからない場合は「SKU \<requested_sku\> not found in the product catalog...」と定型文で返し、代替提案をしない

> ⚠️ ナレッジ ID の指定方法は環境（SaaS / Developer Edition）や ADK バージョンによって異なります。YAML の `knowledge` セクションは実環境に合わせた調整が必要な場合があります。

---

### Step 10: Store Associate Agent（スーパーバイザー）の作成

```bash
orchestrate agents import -f Store_Associate_Agent.yaml
```

定義の要点（`Store_Associate_Agent.yaml`）:

- `agents`: `SKU_Availability_Agent`, `Substitute_Finder_Agent` を配下に持つ
- 実行ルール:
  1. 店舗名が指定されていなければ、店舗名を聞き返して停止
  2. `SKU_Availability_Agent` で在庫照会
  3. 在庫 > 0 → 「在庫あり」＋数量を回答
  4. 在庫 = 0 → `Substitute_Finder_Agent` を呼び、代替品を最大 3 件と理由を回答
  5. **Kafka / MCP / ksqlDB / Flink や内部エージェント名を最終回答に出さない**
- 回答は店舗スタッフが顧客にそのまま伝えられる短さにする

> ステップ 5 の「内部実装名を出さない」制約は、デモで**エンドユーザー体験と内部アーキテクチャの分離**を説明する良い題材です。

---

## 4. デモシナリオ（実演台本）

`Store_Associate_Agent` のチャットで以下を順に実行します。

| # | 入力 | 期待される挙動 | 見せどころ |
|---|---|---|---|
| 1 | `Do you have LAPTOP-DELL-XPS-15?` | 店舗名を聞き返す | 必須情報の確認ルールが効いている |
| 2 | `Mall of Egypt` / `Do you have LAPTOP-HP-SPECTRE-X360 in Dubai Mall?` | 「在庫あり（52台）」と回答 | Kafka → ksqlDB → MCP のリアルタイム経路 |
| 3 | `Do you have LAPTOP-DELL-XPS-15 in Dubai Mall?` | 在庫 0 を検出 → 代替品を最大 3 件提案 | マルチエージェント連携 + 文書グラウンディング |
| 4 | （別ターミナルで追加トランザクションを投入し）再度同じ質問 | 回答が新しい在庫状態に変わる | **イベント駆動でエージェントの結論が更新される** |

**#4 の準備**: `sample-transactions.json` に `LAPTOP-DELL-XPS-15` / `DubaiMall` の入庫レコードを追記し、`produce_messages.py` を再実行すると在庫が復活します（ksqlDB の集計に反映されるまで数秒待つ）。

**#3 の追い打ち質問**（提案の根拠を見せる）:
- 「Why did you recommend that model?」→ カタログ上の共通属性を根拠に説明する

---

## 5. 付録

### 5.1 対象 SKU / 店舗

**SKU**
- LAPTOP-DELL-XPS-15
- LAPTOP-HP-SPECTRE-X360
- LAPTOP-MACBOOK-PRO-16
- MOBILE-IPHONE-17-PRO-MAX
- MOBILE-SAMSUNG-S24-ULTRA
- MOBILE-GOOGLE-PIXEL-8-PRO

**店舗（branch）**
- `DubaiMall`
- `MallOfEgypt`

> エージェントの instructions では `DubaiMall` / `MallOfEgypt`（スペースなし）で定義されています。ユーザー入力の「Dubai Mall」「Mall of Egypt」を正規化できるかは LLM 依存のため、デモ前に必ず実機確認してください。

### 5.2 ファイル一覧（`confluent-agents/`）

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

### 5.3 トラブルシューティング

| 症状 | 確認ポイント |
|---|---|
| `Missing required environment variables` | `.env` の必須項目（`BOOTSTRAP_SERVERS` / `KAFKA_API_KEY` / `KAFKA_API_SECRET`）を確認 |
| `Please update .env file with your actual ... credentials` | テンプレートの `xxxxx` や `your-kafka-api-key` が残っている |
| MCP ツールが空結果を返す | ksqlDB のテーブル名が `INVENTORY_AVAILABILITY` か、トピックにデータが入っているかを確認 |
| ksqlDB 認証エラー | ksqlDB 用 API Key は Kafka クラスター用とは**別**。混同していないか確認 |
| Orchestrate でツールが見つからない | `--package-root` が絶対パスになっているか確認 |
| 在庫が更新されない | ksqlDB の集計反映待ち。数秒後に再照会 |

### 5.4 拡張アイデア（原典より）

追加の Kafka トピックを購読させることで、プロモーション情報、サプライヤー遅延、需要予測などの別のリアルタイムシグナルと在庫データを突き合わせた推論に拡張できます。

---

## 6. 確認事項（原典で要確認）

本書作成時、原典ページはクライアントサイドレンダリングのため本文を機械取得できませんでした。以下は **GitHub リポジトリの実物から確認済みの情報 + 推定**で構成しています。デモ実施前に原典で照合してください。

**確認済み（リポジトリ実物ベース）**
- 全 Python スクリプトの挙動、環境変数名、ツール仕様
- 3 つのエージェント YAML の定義内容
- サンプルデータの内容と集計結果
- `orchestrate toolkits add` コマンド（原典本文の該当箇所も取得済み）

**推定・要確認**
- ksqlDB の `CREATE STREAM` / `CREATE TABLE` の正確な DDL（Step 5）
- Confluent Cloud / watsonx Orchestrate の UI 操作の細部
- Substitute Finder Agent のナレッジ登録手順（UI 操作 or CLI）
- `orchestrate agents import` の正確なオプション（ADK バージョン依存）
- 原典で紹介されている IBM Bob を用いた開発フローの詳細
  （関連チュートリアル: "Using IBM Bob to build watsonx Orchestrate agents and MCP tools"）
