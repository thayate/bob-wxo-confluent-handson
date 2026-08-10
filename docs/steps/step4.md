# ステップ4：MCPツールとAIエージェントを watsonx Orchestrate に作成する

## 概要

このステップでは、Kafka の在庫データをリアルタイムで照会する **MCP ツール**と、それを使用する **SKU Availability Agent** を watsonx Orchestrate に作成します。

MCP ツールとエージェントの設定は IBM Bob の助けを借りて作成・検証されています。

!!! info "参考ハンズオン"
    Bob を使った MCP ツールとエージェントの作成について詳しくは、[IBM Bob を使用した watsonx Orchestrate エージェントと MCP ツールの構築](https://developer.ibm.com/tutorials/build-agents-mcp-tools-watsonx-orchestrate-using-bob/)ハンズオンを参照してください。

---

## 手順

### 1. .env ファイルに ksqlDB の認証情報を追加する

MCP ツールは ksqlDB クラスターと通信するため、`.env` ファイルに ksqlDB の認証情報を追加します。

```bash
# .env ファイルに追加する項目
KSQL_ENDPOINT=https://pksqlc-xxxxx.us-east-1.aws.confluent.cloud
KSQL_API_KEY=your_ksqldb_api_key
KSQL_API_SECRET=your_ksqldb_api_secret
```

ksqlDB クラスターの詳細は、Confluent UI の **ksqlDB** > クラスター選択 > **Cluster settings** から確認できます。

---

### 2. MCP ツールを watsonx Orchestrate にインポートする

以下のコマンドを実行して MCP ツールをインポートします。

`/path/to/oic-i-agentic-ai-tutorials/confluent-agents` の部分は、**自分のマシンでリポジトリをクローンした絶対パス**に置き換えてください。

```bash
orchestrate toolkits add \
  --kind mcp \
  --name "sku-availability-checker" \
  --description "Real-time inventory availability checker using Confluent Kafka and ksqlDB" \
  --language python \
  --package-root "/path/to/oic-i-agentic-ai-tutorials/confluent-agents" \
  --command "python3 get_sku_availability.py" \
  --tools "*"
```

!!! warning "パスについて"
    `--package-root` には必ず**絶対パス**を指定してください。相対パスは使用できません。

インポートに成功すると、以下のような出力が表示されます：

```
Toolkit 'sku-availability-checker' added successfully.
```

---

### 3. エージェント定義ファイルをインポートする

リポジトリに含まれている YAML 定義ファイルを使ってエージェントをインポートします。

```bash
cd oic-i-agentic-ai-tutorials/confluent-agents
orchestrate agents import -f SKU_Availability_Agent.yaml
```

---

### 4. エージェントを確認する

1. watsonx Orchestrate UI を開く
2. **Manage agents** に移動する
3. **SKU_Availability_Agent** をクリックする
4. MCP ツール（`sku-availability-checker`）が紐付けられていることを確認する
5. エージェントの動作設定（プロンプト、ツール設定など）を確認する

---

### 5. エージェントをテストする

エージェントのチャットインターフェースで以下を入力してテストします：

```
What are the available SKUs in Mall of Egypt?
```

**期待される動作：**

1. エージェントが `get_sku_availability` ツールを呼び出す
2. ksqlDB から在庫データを取得する
3. MallOfEgypt の各 SKU の在庫数量を一覧で返す

**期待される回答例：**
```
Here are the available SKUs in Mall of Egypt:
- LAPTOP-HP-SPECTRE-X360: 5 units available
- LAPTOP-MACBOOK-PRO-16: 3 units available
- LAPTOP-DELL-XPS-15: 0 units (out of stock)
- MOBILE-IPHONE-15: 6 units available
...
```

---

## SKU_Availability_Agent の動作について

このエージェントは以下のロジックで動作します：

```
ユーザーの質問を解析
    ↓
SKU と ブランチを抽出
    ↓
get_sku_availability(sku, branch) を呼び出す
    ↓
ksqlDB が inventory.availability から現在の在庫状況を返す
    ↓
結果を自然言語でユーザーに返す
```

---

## 確認

- [ ] `.env` ファイルに ksqlDB の認証情報が設定されている
- [ ] `orchestrate toolkits add` コマンドが正常に完了している
- [ ] watsonx Orchestrate UI に `SKU_Availability_Agent` が表示されている
- [ ] エージェントのテストで在庫情報が返ってくる

---

## 次のステップ

SKU Availability Agent の作成が完了したら、[ステップ5](step5.md) に進んで Agentic RAG エージェントを作成します。
