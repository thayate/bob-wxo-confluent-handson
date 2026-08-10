# トラブルシューティング

よくある問題とその解決策をまとめています。

---

## Confluent Cloud 関連

### ksqlDB クラスターが `Provisioning` から変わらない

**症状：** `confluent ksql cluster describe {KSQL_CLUSTER_ID}` を実行しても `Status: Provisioning` のまま。

**解決策：** プロビジョニングには通常 2〜5 分かかります。数分待ってから再度確認してください。

```bash
# 繰り返し確認する場合
watch -n 10 confluent ksql cluster describe {KSQL_CLUSTER_ID}
```

---

### ksqlDB でストリーム/テーブル作成が失敗する

**症状：** `CREATE STREAM` や `CREATE TABLE` クエリを実行すると `Could not connect to the Kafka cluster` エラーが出る。

**解決策：**
1. ksqlDB クラスターのステータスが `Provisioned` になっていることを確認する
2. ksqlDB クラスターの API キーが正しく設定されていることを確認する
3. Confluent UI を更新（リロード）してから再試行する

---

### `inventory.availability` トピックが作成されない

**症状：** `CREATE TABLE inventory_availability ...` を実行したが、トピック一覧に `inventory.availability` が表示されない。

**解決策：**
1. まず `SELECT * FROM INVENTORY_AVAILABILITY EMIT CHANGES;` でテーブルが存在するか確認する
2. テーブルが存在する場合は、トピックが自動作成されるまで 1〜2 分待つ
3. Confluent UI を更新する

---

### API キーが機能しない

**症状：** メッセージ投入スクリプトを実行すると認証エラーが発生する。

**解決策：**
1. `.env` ファイルの `API_KEY` と `API_SECRET` に余分な空白や改行が入っていないか確認する
2. API キーが正しい**クラスター**（Kafka クラスター用）に対して作成されているか確認する
3. Confluent Cloud UI で API キーが有効になっているか確認する

---

## MCP ツール関連

### `orchestrate toolkits add` コマンドが失敗する

**症状：** `Error: package-root path does not exist` などのエラーが出る。

**解決策：**
1. `--package-root` に**絶対パス**を指定しているか確認する（`~` の展開が必要な場合がある）
2. 指定したパスに `get_sku_availability.py` ファイルが存在するか確認する

```bash
# パスを確認する
ls -la /absolute/path/to/confluent-agents/
```

---

### MCP ツールが ksqlDB に接続できない

**症状：** エージェントがツールを呼び出すと `Connection refused` や `Unauthorized` エラーが返ってくる。

**解決策：**
1. `.env` ファイルに `KSQL_ENDPOINT`、`KSQL_API_KEY`、`KSQL_API_SECRET` が設定されているか確認する
2. `KSQL_ENDPOINT` の末尾にスラッシュ（`/`）が入っていないか確認する
3. ksqlDB クラスター用の API キー（Kafka クラスター用ではない）を使っているか確認する

---

## watsonx Orchestrate エージェント関連

### エージェントのインポートに失敗する

**症状：** `orchestrate agents import -f Agent.yaml` を実行するとエラーが出る。

**解決策：**
1. watsonx Orchestrate ADK が起動しているか確認する
2. `orchestrate env activate` でアクティブな環境を確認する
3. YAML ファイルのパスが正しいか確認する

```bash
# 環境の状態を確認する
orchestrate env list
```

---

### Substitute Finder Agent が代替品を推薦しない

**症状：** Substitute Finder Agent に「在庫がない SKU の代替品を探して」と指示しても、ドキュメントを参照せずにエラーや空の回答を返す。

**解決策：**
1. `enterprise_documents` ナレッジソースのインデックス処理が完了しているか確認する
2. ナレッジソースの名前が正確に `enterprise_documents` になっているか確認する（大文字小文字を含む）
3. エージェントをいったんアンデプロイして再デプロイする

---

### Store Associate Agent が専門エージェントに委任しない

**症状：** Store Associate Agent が在庫確認や代替品推薦を自身で行おうとする（またはエラーを返す）。

**解決策：**
1. `SKU_Availability_Agent` と `Substitute_Finder_Agent` の両方がデプロイ済みでアクティブ状態になっているか確認する
2. `Store_Associate_Agent.yaml` の `collaborators` セクションに両エージェントが正しく定義されているか確認する
3. エージェントをいったんアンデプロイして再デプロイする

---

## Python スクリプト関連

### `produce_messages.py` 実行時にエラーが出る

**症状：** `ModuleNotFoundError: No module named 'confluent_kafka'` などのエラー。

**解決策：**

```bash
# 依存パッケージをインストールする
pip install -r requirements.txt

# 仮想環境を使っている場合
python -m pip install -r requirements.txt
```

---

### メッセージが投入されるが ksqlDB に反映されない

**症状：** Confluent UI でメッセージ数が 20 になっているが、ksqlDB の `INVENTORY_AVAILABILITY` テーブルが空。

**解決策：**
1. ksqlDB エディタで `auto.offset.reset` が **Earliest** に設定されているか確認する
2. `SELECT * FROM INVENTORY_AVAILABILITY EMIT CHANGES;` を再実行する
3. `inventory_transactions` ストリームと `inventory_availability` テーブルが作成されているか確認する

```sql
-- 作成済みのストリームとテーブルを確認する
SHOW STREAMS;
SHOW TABLES;
```

---

## その他

問題が解決しない場合は、以下のリソースを参照してください：

- [Confluent Cloud サポート](https://support.confluent.io/)
- [watsonx Orchestrate ドキュメント](https://www.ibm.com/docs/en/watsonx/watson-orchestrate/)
- [元のハンズオン（英語）](https://developer.ibm.com/tutorials/event-driven-agentic-ai-system-confluent-watsonx-orchestrate/)
