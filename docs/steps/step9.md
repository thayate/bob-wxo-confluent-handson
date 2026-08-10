# Step 9: Substitute Finder Agent の作成

在庫がない SKU に対して、**製品カタログ文書に基づいて**代替品を提案する専門エージェントです。

1. watsonx Orchestrate でナレッジ（Knowledge base）を作成し、`product-catalog.docx` をアップロード
2. エージェントをインポート

```bash
orchestrate agents import -f Substitute_Finder_Agent.yaml
```

## 定義の要点（`Substitute_Finder_Agent.yaml`）

| 項目 | 値 |
|---|---|
| `knowledge` | `enterprise_documents` / `semantic_similarity` / `top_k: 5` |

**挙動ルール:**

- 必ず製品カタログ文書を根拠にする
- 文書外の一般知識に依存しない／製品や仕様を捏造しない
- 抽出する属性: カテゴリ、ブランド、フォームファクター、画面サイズ、プロセッサクラス、メモリ、ストレージ、用途、主要機能
- 類似度でランキングし、**最大 3 件**を提案
- 見つからない場合は `SKU <requested_sku> not found in the product catalog...` と定型文で返し、代替提案をしない

!!! warning "注意"
    ナレッジ ID の指定方法は環境（SaaS / Developer Edition）や ADK バージョンによって異なります。YAML の `knowledge` セクションは実環境に合わせた調整が必要な場合があります。
