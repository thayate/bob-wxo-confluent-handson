# Step 3: `.env` ファイルの作成

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

!!! warning "注意"
    `.env` は `.gitignore` 済み。デモ用資材としても Git にコミットしないこと。
