# Step 10: Store Associate Agent（スーパーバイザー）の作成

```bash
orchestrate agents import -f Store_Associate_Agent.yaml
```

## 定義の要点（`Store_Associate_Agent.yaml`）

| 項目 | 値 |
|---|---|
| `agents` | `SKU_Availability_Agent`, `Substitute_Finder_Agent` を配下に持つ |

**実行ルール:**

1. 店舗名が指定されていなければ、店舗名を聞き返して停止
2. `SKU_Availability_Agent` で在庫照会
3. 在庫 > 0 → 「在庫あり」＋数量を回答
4. 在庫 = 0 → `Substitute_Finder_Agent` を呼び、代替品を最大 3 件と理由を回答
5. **Kafka / MCP / ksqlDB / Flink や内部エージェント名を最終回答に出さない**

回答は店舗スタッフが顧客にそのまま伝えられる短さにする。

!!! tip "デモの見せどころ"
    ステップ 5 の「内部実装名を出さない」制約は、デモで**エンドユーザー体験と内部アーキテクチャの分離**を説明する良い題材です。
