# 前提条件

ハンズオンを開始する前に、以下の環境・アカウントを準備してください。

---

## 必須要件

### 1. watsonx Orchestrate ADK（ローカル環境）

このハンズオンは、ローカル環境で **watsonx Orchestrate Agent Development Kit (ADK)** が動作していることを前提としています。

まだセットアップしていない場合は、[ADK 入門ハンズオン](https://developer.ibm.com/tutorials/getting-started-with-watsonx-orchestrate/)を参照して環境を構築してください。

!!! warning "バージョン要件"
    本ハンズオンは watsonx Orchestrate ADK バージョン **2.1** で動作検証済みです。

### 2. watsonx Orchestrate インスタンス

[watsonx Orchestrate のインスタンス](https://www.ibm.com/docs/en/watsonx/watson-orchestrate/base?topic=orchestrate-accessing-trial-version)を用意してください。

### 3. Confluent Cloud アカウント

Apache Kafka へのアクセス権を持つ **Confluent Cloud** アカウントが必要です。

アカウントをお持ちでない場合は、[Confluent Cloud 登録ページ](https://confluent.cloud/signup)から無料アカウントを作成してください。

### 4. Kafka の基礎知識

以下の Kafka 基本概念を理解していることを前提としています：

- **トピック（Topic）**：イベントを格納するカテゴリ
- **コンシューマー（Consumer）**：トピックからメッセージを読み取る側
- **プロデューサー（Producer）**：トピックへメッセージを書き込む側

---

## オプション要件

### IBM Bob（推奨）

[IBM Bob](https://bob.ibm.com/trial/?utm_source=developer-content&cm_sp=ibmdev-_-developer-_-trial) は、自然言語でコードや設定ファイルを生成できる AI 開発パートナーです。

各ステップで Bob を使った方法と手動での方法を両方紹介しています。Bob を使うことで各ステップを大幅に短縮できます。

[無料トライアルに登録する](https://bob.ibm.com/trial/?utm_source=developer-content&cm_sp=ibmdev-_-developer-_-trial){ .md-button .md-button--primary }

---

## 必要なソフトウェア

| ソフトウェア | バージョン | 用途 |
|-------------|-----------|------|
| Python | 3.8 以上 | サンプルメッセージ投入スクリプト実行 |
| Confluent CLI | 最新版 | ksqlDB クラスター作成 |
| Git | 任意 | リポジトリのクローン |

### Confluent CLI のインストール

```bash
# macOS (Homebrew)
brew install confluentinc/tap/cli

# その他のプラットフォームは公式ドキュメントを参照
# https://docs.confluent.io/confluent-cli/current/install.html
```

---

## 事前確認チェックリスト

- [ ] watsonx Orchestrate ADK がローカルで起動している
- [ ] watsonx Orchestrate インスタンスへのアクセスが確認できている
- [ ] Confluent Cloud アカウントにログインできる
- [ ] Python 3.8 以上がインストールされている
- [ ] Confluent CLI がインストールされている

すべて確認できたら、[アーキテクチャ概要](overview.md)に進んでください。
