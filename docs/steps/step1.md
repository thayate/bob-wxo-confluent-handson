# ステップ1：Confluent Cloud に Kafka トピックを作成する

## 概要

このステップでは、在庫トランザクションを格納するための Kafka トピック `inventory.transactions` を作成します。

---

## Bob への指示例

Bob に以下のように指示してください：

```
Hi Bob, create a python code to create a topic on Confluent Cloud 
called "inventory.transactions", make the number of partitions and 
retention configurable, and create an .env file and I will fill it 
with my Confluent Cloud details and credentials. 
Use "~/Documents/bob/confluent-agents" as my working directory for the project.
```

Bob が Python スクリプトと `.env` ファイルを生成します。

## .env ファイルへの認証情報入力

Bob が生成した `.env` ファイルを開いて、Confluent Cloud の認証情報を入力してください。

入力後、Bob に以下のように指示してトピックを作成します：

```
Done, I edited the .env file with my credentials, you can now create 
the topic on Confluent Kafka, and please validate that it's created 
successfully.
```

Bob が自動的にトピックを作成し、作成確認まで行います。

---

## 確認

トピックの作成が完了したら、以下を確認してください：

- [ ] `inventory.transactions` トピックが Topics 一覧に表示されている
- [ ] パーティション数が 1 になっている
- [ ] 保持期間が Infinite（無制限）に設定されている

---

## 次のステップ

トピックの作成が完了したら、[ステップ2](step2.md) に進んで派生トピックを作成します。
