# 前提条件

ハンズオンを開始する前に、以下の環境・アカウントを準備してください。

---

## 必須要件

### 1. IBM Bob
アカウントとインストール済みの環境<br>
まだお持ちでない方は[30日間の無料トライアル](https://r-nakayamasan.github.io/bob-workshop-draft/00_introduction/#:~:text=%E3%82%A2%E3%82%AB%E3%82%A6%E3%83%B3%E3%83%88%E3%81%A8%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB,%E3%81%93%E3%81%A1%E3%82%89%E3%82%92%E5%8F%82%E7%85%A7)から登録<br>
登録ガイドは[こちら](https://r-nakayamasan.github.io/bob-workshop-draft/00_introduction/#:~:text=%E3%82%A2%E3%82%AB%E3%82%A6%E3%83%B3%E3%83%88%E3%81%A8%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB,%E3%81%93%E3%81%A1%E3%82%89%E3%82%92%E5%8F%82%E7%85%A7)を参照

### 2. watsonx Orchestrate ADK（ローカル環境）

このハンズオンは、ローカル環境で **watsonx Orchestrate Agent Development Kit (ADK)** が動作していることを前提としています。

!!! warning "バージョン要件"
    本ハンズオンは watsonx Orchestrate ADK バージョン **2.1** で動作検証済みです。

### 3. watsonx Orchestrate インスタンス

[watsonx Orchestrate のインスタンス](https://www.ibm.com/docs/en/watsonx/watson-orchestrate/base?topic=orchestrate-accessing-trial-version)を用意してください。

### 4. Confluent Cloud アカウント

Apache Kafka へのアクセス権を持つ **Confluent Cloud** アカウントが必要です。

アカウントをお持ちでない場合は、[Confluent Cloud 登録ページ](https://confluent.cloud/signup)から無料アカウントを作成してください。

### 5. Kafka の基礎知識

以下の Kafka 基本概念を理解していることを前提としています：

- **トピック（Topic）**：イベントを格納するカテゴリ
- **コンシューマー（Consumer）**：トピックからメッセージを読み取る側
- **プロデューサー（Producer）**：トピックへメッセージを書き込む側

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
