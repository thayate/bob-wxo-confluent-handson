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
KSQLDB_ENDPOINT=https://pksqlc-xxxxx.us-east-1.aws.confluent.cloud
KSQLDB_API_KEY=your_ksqldb_api_key
```

ksqlDB クラスターの詳細は、Confluent UI の **ksqlDB** > クラスター選択 > **Settings** から確認できます。<br>
![alt text](<step4_images/スクリーンショット 2026-08-11 9.03.44.png>)
---

### 2. MCP ツールを watsonx Orchestrate にインポートする

watsonx Orchestrateで使うMCPツール群やエージェント定義ファイルを[ダウンロード](https://downgit.github.io/#/home?url=https://github.com/thayate/bob-wxo-confluent-handson/tree/f14e83d023900b3b0ffd16cc7c4a1da18ebcf9c0/resource)します。<br>

ファイルの内容は以下で確認できます。<br>
[https://github.com/thayate/bob-wxo-confluent-handson/tree/f14e83d023900b3b0ffd16cc7c4a1da18ebcf9c0/resource](https://github.com/thayate/bob-wxo-confluent-handson/tree/f14e83d023900b3b0ffd16cc7c4a1da18ebcf9c0/resource)

ダウンロードしたファイルはプロジェクトフォルダに`resources`というフォルダを作成して格納してください。

以下のコマンドを実行して MCP ツールをインポートします。<br>
ユニークな名称にするため、ご自身のイニシャルや名前を追加する指示をしてください。

Bobに以下の指示をしてください。
```bash
watsonx OrchestrateのADKで以下のコマンドを実行してください。
インポートするツールに"ご自身のイニシャル_"を追加してユニークな名称にしてください。
orchestrate toolkits add \
  --kind mcp \
  --name "sku-availability-checker" \
  --description "Real-time inventory availability checker using Confluent Kafka and ksqlDB" \
  --language python \
  --package-root "resources" \
  --command "python3 get_sku_availability.py" \
  --tools "*"
```

---

### 3. エージェント定義ファイルをインポートする

リポジトリに含まれている YAML 定義ファイルを使ってエージェントをインポートします。

```bash
resources/sku-availability-agent.yamlの名称に
"ご自身のイニシャル_"を付け加えて、watsonx Orchestrateにインポートしてください
```

---

### 4. エージェントを確認する

1. watsonx Orchestrate UI を開く
2. **ビルド** に移動する<br>
![alt text](<step4_images/スクリーンショット 2026-08-11 13.22.05.png>)

3. **[ご自身のイニシャル]_SKU_Availability_Agent** をクリックする
4. MCP ツール（`sku-availability-checker`）が紐付けられていることを確認する<br>
![alt text](<step4_images/スクリーンショット 2026-08-11 13.57.53.png>)

5. エージェントの動作設定（プロンプト、ツール設定など）を確認する<br>
![alt text](<step4_images/スクリーンショット 2026-08-11 13.58.19.png>)

---

### 5. エージェントをテストする

エージェントのチャットインターフェースで以下を入力してテストします：

```
Mall of Egyptで入手可能なSKUは何ですか?
```

**期待される動作：**

1. エージェントが `get_sku_availability` ツールを呼び出す
2. ksqlDB から在庫データを取得する
3. MallOfEgypt の各 SKU の在庫数量を一覧で返す

**期待される回答例：**
![alt text](<step4_images/スクリーンショット 2026-08-11 13.55.09.png>)
---

## SKU_Availability_Agent の動作について

このエージェントは以下のロジックで動作します：

```
ユーザーの質問を解析
    ↓
SKU と 支店を抽出
    ↓
get_sku_availability(sku, branch) を呼び出す
    ↓
ksqlDB が inventory.availability から現在の在庫状況を返す
    ↓
結果を自然言語でユーザーに返す
```


## 次のステップ

SKU Availability Agent の作成が完了したら、[ステップ5](step5.md) に進んで Agentic RAG エージェントを作成します。
