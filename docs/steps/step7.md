# Step 7: MCP ツールを watsonx Orchestrate に取り込む

`--package-root` は**プロジェクトの絶対パス**に置き換えてください。

```bash
orchestrate toolkits add \
  --kind mcp \
  --name "sku-availability-checker" \
  --description "Real-time inventory availability checker using Confluent Kafka and ksqlDB" \
  --language python \
  --package-root "/absolute/path/to/oic-i-agentic-ai-tutorials/confluent-agents" \
  --command "python3 get_sku_availability.py" \
  --tools "*"
```
