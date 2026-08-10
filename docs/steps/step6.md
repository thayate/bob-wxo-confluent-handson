# Step 6: MCP ツールのローカル検証

`get_sku_availability.py` は FastMCP ベースの MCP サーバーです。

| 項目 | 内容 |
|---|---|
| ツール名 | `get_sku_availability` |
| 引数 | `sku`（任意）, `branch`（任意）— 空文字ならフィルタなし |
| 動作 | ksqlDB の `/query-stream` エンドポイントに Basic 認証で POST し、`INVENTORY_AVAILABILITY` を検索 |
| 戻り値 | `{"results": [{"sku": ..., "branch": ..., "available_quantity": ...}]}` の JSON 文字列 |

ローカルテスト:

```bash
python3 test_mcp_client.py
```

テストクライアントは以下 4 パターンを実行します。

1. 全件取得
2. SKU 指定（`LAPTOP-DELL-XPS-15`）
3. 店舗指定（`DubaiMall`）
4. SKU + 店舗指定
