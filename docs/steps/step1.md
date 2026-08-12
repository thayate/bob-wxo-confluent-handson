# ステップ1：Confluent Cloud に Kafka トピックを作成する

## 概要

このステップでは、在庫トランザクションを格納するための Kafka トピック `inventory.transactions` を作成します。

---
## Bobの設定
### 作業フォルダの作成
Bobで作業するためのプロジェクトフォルダを作成します。<br>
好きな場所に"confluent-agents"という名称のフォルダを作成してください。ハンズオンの作業フォルダとして使用します。<br>
### プロジェクトフォルダを開く
1. デスクトップまたはアプリケーションフォルダから、IBM Bobを起動します。
2. IBM Bob左上の「ファイル」メニューをクリック
3. 「フォルダーを開く」を選択<br>
![alt text](<step1_images/スクリーンショット 2026-08-10 23.02.21.png>)
4. 先ほどコピーした「confluent-agents」フォルダに移動<br>
5. 「開く」をクリック（OSによって画面イメージや項目は異なります）<br>
![alt text](<step1_images/スクリーンショット 2026-08-10 23.03.13.png>)
6. 以下のように作業フォルダとBobのチャット画面が立ち上がっていたら成功です。<br>
制限モードになっている場合はその作業フォルダを「信頼する」設定を実施してください。 
![alt text](<step1_images/スクリーンショット 2026-08-10 23.06.11.png>)

## .env.example ファイルの作成
プロジェクトフォルダにConfluentの認証情報と設定情報を記述する`.env.example`というファイルを作成してください。<br>
`.env.example`には以下の内容をコピペしてください。


```title=".env.example"
# ============================================================
# Confluent Cloud 認証情報 — .env.example
# このファイルをコピーして .env にリネームし、実際の値を入力してください。
# ============================================================
# --- Confluent Cloud REST API（クラスター管理用）---

# REST エンドポイント（例: https://pkc-xxxxx.ap-northeast-1.aws.confluent.cloud:443）
CONFLUENT_REST_ENDPOINT=https://pkc-xxxxx.ap-northeast-1.aws.confluent.cloud:443

# クラスター ID（例: lkc-xxxxxx）
CONFLUENT_CLUSTER_ID=lkc-xxxxxx

# --- Confluent Cloud クラスター設定 ---
# Bootstrap サーバー（例: pkc-xxxxx.ap-northeast-1.aws.confluent.cloud:9092）
CONFLUENT_BOOTSTRAP_SERVERS=pkc-xxxxx.ap-northeast-1.aws.confluent.cloud:9092

# Kafka API キー / シークレット（クラスタースコープ）
CONFLUENT_API_KEY=FTxxxxxxx
CONFLUENT_API_SECRET=cfltxxxxxxxxxxxxx

# --- Cloud スコープの API Key / Secret（ksqlDB クラスター作成に必要）---
# 取得手順: Confluent Cloud Console 右上のメニュー(Administration) > API keys
#           > Add API key > My account > Resource scope: Global > Next
CONFLUENT_CLOUD_API_KEY=EYC5xxxxxxxx
CONFLUENT_CLOUD_API_SECRET=cfltxxxxxxxxxxxxx

# --- トピック設定 ---
# トピック名（デフォルト: inventory.transactions）
TOPIC_NAME=inventory.transactions

# パーティション数（デフォルト: 6）
TOPIC_PARTITIONS=1

# メッセージ保持期間（ミリ秒）
# 例: 604800000 = 7日間 / 86400000 = 1日間 / -1 = 無期限
TOPIC_RETENTION_MS=604800000

# レプリケーション係数（Confluent Cloud は通常 3）
TOPIC_REPLICATION_FACTOR=3

```

実際の認証情報は後ほど埋めていきます。

## Bob にトピック作成のためのスクリプト作成を指示する

以降の手順ではBobのモードは`Agent`モードで実施します。<br>
Bob に以下のように指示してください：

```
Confluent Cloud上に「inventory.transactions」という名前の
トピックを作成するPythonコードを作成してください。
Confluentの詳細情報と認証情報は.env.exampleにある項目を使用します。
```

Bob が Python スクリプトを生成します。


## .env ファイルへの認証情報入力

### 必要な環境変数と取得場所
1. **CONFLUENT_BOOTSTRAP_SERVERS / CONFLUENT_REST_ENDPOINT / CONFLUENT_CLUSTER_ID**

    https://confluent.cloud にログイン
    左メニューの Environments → 対象の Environment を選択
    ![alt text](<step1_images/スクリーンショット 2026-08-10 23.17.24.png>)

    対象の Cluster をクリック
    ![alt text](<step1_images/スクリーンショット 2026-08-10 23.34.52.png>)

    変数を.envファイルにコピペします。
    ![alt text](<step1_images/スクリーンショット 2026-08-10 23.23.36-1.png>)
<br><br>

2. **CONFLUENT_API_KEY / CONFLUENT_API_SECRET**<br>

    "APY Keys"タブをクリックし、"Create Key"をクリック
    ![alt text](<step1_images/スクリーンショット 2026-08-10 23.28.32-2.png>)

    "My account"を選択
    ![alt text](<step1_images/スクリーンショット 2026-08-10 23.28.38.png>)

    変数を.envファイルにコピペします。
    ![alt text](<step1_images/スクリーンショット 2026-08-10 23.29.05.png>)
<br><br>

3. **CONFLUENT_CLOUD_API_KEY / CONFLUENT_CLOUD_API_SECRET**<br>

    前述のCONFLUENT_API_KEYと名前が似ていてややこしいのですが、それとは別のキーとシークレットが必要になります。<br>
    Confluent Cloud Console 右上のメニュー(Administration) > API keys > <br>
    適当なAPI Keyの名前を入力 > Select Accout: **My account** > Select key scope: **Global** > Create API Keyから取得可能です<br>
    ![alt text](<step2_images/スクリーンショット 2026-08-11 1.12.26.png>)
    ![alt text](<step2_images/スクリーンショット 2026-08-11 1.12.56.png>)
---

## Bob にトピック作成を実行してもらう
環境変数を入力後、Bob に以下のように指示してトピックを作成します：

```
.env ファイルに私の認証情報を追加したので、Confluent Kafka 上にトピックを作成してください。
```

完了するとConfluent Cloud上で以下のようにトピックが作成されていることが確認できます。
![alt text](<step1_images/スクリーンショット 2026-08-11 0.07.43.png>)
---

## 次のステップ

トピックの作成が完了したら、[ステップ2](step2.md) に進んで派生トピックを作成します。
