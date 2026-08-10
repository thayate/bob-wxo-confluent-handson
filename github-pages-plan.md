# GitHub Pages ハンズオンガイド構築計画

## 概要

`confluent-wxo-event-driven-agent-demo-手順書.md` をもとに、MkDocs Material を使った GitHub Pages サイトを
`https://github.com/thayate/bob-wxo-confluent-handson` に構築する。

- **言語**: 日本語のみ
- **フレームワーク**: MkDocs + Material for MkDocs
- **デプロイ**: GitHub Actions（gh-pages ブランチへの自動デプロイ）
- **ページ分割**: セクションごとに個別 Markdown ファイルへ分割

---

## サイト構成

```
docs/
├── index.md                    # トップ（概要）
├── overview.md                 # デモ概要・アーキテクチャ・訴求ポイント
├── prerequisites.md            # 前提条件
├── steps/
│   ├── step1.md               # Step 1: リポジトリ取得と環境準備
│   ├── step2.md               # Step 2: Confluent Cloud クラスター作成
│   ├── step3.md               # Step 3: .env ファイル作成
│   ├── step4.md               # Step 4: トピック作成とサンプルデータ投入
│   ├── step5.md               # Step 5: ksqlDB クラスター作成と集計定義
│   ├── step6.md               # Step 6: MCP ツール検証
│   ├── step7.md               # Step 7: MCP ツールを watsonx Orchestrate に取り込む
│   ├── step8.md               # Step 8: SKU Availability Agent インポート
│   ├── step9.md               # Step 9: Substitute Finder Agent 作成
│   └── step10.md              # Step 10: Store Associate Agent 作成
├── demo-scenario.md            # デモシナリオ（実演台本）
└── appendix/
    ├── sku-list.md            # 5.1 対象 SKU / 店舗
    ├── file-list.md           # 5.2 ファイル一覧
    ├── troubleshooting.md     # 5.3 トラブルシューティング
    └── extensions.md          # 5.4 拡張アイデア
```

---

## サブタスク

---

### サブタスク 1: MkDocs 設定ファイルと GitHub Actions ワークフローの作成

**Intent**
プロジェクトの土台となる `mkdocs.yml` と `.github/workflows/deploy.yml` を作成し、GitHub Actions によって自動デプロイが動作する状態にする。

**Expected Outcomes**
- `mkdocs.yml` が Material テーマ・日本語設定・ナビゲーション構造を持つ
- `.github/workflows/deploy.yml` が `main` ブランチへのプッシュ時に `gh-pages` ブランチへ自動デプロイする
- `README.md` に GitHub Pages の URL と簡単な説明が記載される

**Todo List**
1. `mkdocs.yml` を作成（サイト名・言語・Material テーマ・ナビゲーション・プラグイン設定）
2. `.github/workflows/deploy.yml` を作成（`actions/checkout`, `mkdocs gh-deploy` ステップ）
3. `requirements.txt` を作成（`mkdocs-material` バージョンを固定）
4. `README.md` を作成（GitHub Pages の URL と概要を記載）

**Relevant Context**
- GitHub Pages URL: `https://thayate.github.io/bob-wxo-confluent-handson/`
- リポジトリ: `https://github.com/thayate/bob-wxo-confluent-handson.git`
- Material for MkDocs の標準 GitHub Actions 設定を使用

**Status**: [ ] pending

---

### サブタスク 2: docs ディレクトリと Markdown ページの作成

**Intent**
手順書の各セクションを個別の Markdown ファイルに分割し、`docs/` ディレクトリに配置する。

**Expected Outcomes**
- `docs/index.md`（トップページ）が作成されている
- `docs/overview.md` にデモ概要・訴求ポイント・アーキテクチャが含まれる
- `docs/prerequisites.md` に前提条件が含まれる
- `docs/steps/step1.md` ～ `docs/steps/step10.md` が各手順を含む
- `docs/demo-scenario.md` にデモシナリオが含まれる
- `docs/appendix/` 配下に付録の各ページが含まれる
- 元の手順書から内容のコピーのみ。追加・改変はしない

**Todo List**
1. `docs/index.md` を作成（タイトル・デモ概要の要約・ナビゲーション案内）
2. `docs/overview.md` を作成（手順書のセクション 1 から）
3. `docs/prerequisites.md` を作成（手順書のセクション 2 から）
4. `docs/steps/step1.md` ～ `docs/steps/step10.md` を作成（手順書の Step 1〜10 から）
5. `docs/demo-scenario.md` を作成（手順書のセクション 4 から）
6. `docs/appendix/sku-list.md` を作成（手順書の 5.1 から）
7. `docs/appendix/file-list.md` を作成（手順書の 5.2 から）
8. `docs/appendix/troubleshooting.md` を作成（手順書の 5.3 から）
9. `docs/appendix/extensions.md` を作成（手順書の 5.4 から）

**Relevant Context**
- ソース: `confluent-wxo-event-driven-agent-demo-手順書.md`
- ページ内容は手順書から忠実に転記する（加筆・改変しない）

**Status**: [ ] pending

---

### サブタスク 3: Git 初期化と GitHub リモートへのプッシュ

**Intent**
作成したファイル群を Git リポジトリとして初期化し、GitHub リポジトリにプッシュして、GitHub Pages のデプロイを起動する。

**Expected Outcomes**
- `git init` 済みでコミットが作成されている
- `origin` として `https://github.com/thayate/bob-wxo-confluent-handson.git` が設定されている
- `main` ブランチへのプッシュ後、GitHub Actions が起動し `gh-pages` ブランチが生成される
- GitHub リポジトリの Settings → Pages で Source が `gh-pages` ブランチに設定されている（手動または Actions 経由）

**Todo List**
1. `.gitignore` を作成（`.venv/`, `site/`, `.env` などを除外）
2. `git init && git add . && git commit -m "initial commit"` を実行
3. `git remote add origin https://github.com/thayate/bob-wxo-confluent-handson.git` を実行
4. `git push -u origin main` を実行
5. GitHub リポジトリの Settings → Pages → Source を `gh-pages` ブランチに設定するよう案内

**Relevant Context**
- リポジトリ: `https://github.com/thayate/bob-wxo-confluent-handson.git`
- GitHub Actions の `mkdocs gh-deploy` コマンドが `gh-pages` ブランチを自動生成する
- GitHub Pages の有効化は UI 操作が必要なため、手順書として案内する

**Status**: [ ] pending
