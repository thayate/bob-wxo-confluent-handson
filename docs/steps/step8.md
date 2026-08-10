# Step 8: SKU Availability Agent のインポート

```bash
orchestrate agents import -f sku-availability-agent.yaml
```

## 定義の要点（`sku-availability-agent.yaml`）

| 項目 | 値 |
|---|---|
| `name` | `SKU_Availability_Agent` |
| `llm` | `groq/openai/gpt-oss-120b` |
| `tools` | `sku-availability-checker:get_sku_availability` |
| instructions | 在庫照会の手順、在庫 0 の明示、対象 SKU・店舗の一覧を明記 |

## 動作確認

watsonx Orchestrate UI → **Manage agents** → `SKU_Availability_Agent` を開き、MCP ツールが紐づいていることを確認してテスト実行。

テスト例:

- `Check availability of LAPTOP-DELL-XPS-15 in DubaiMall`
- `Show all inventory in MallOfEgypt`
