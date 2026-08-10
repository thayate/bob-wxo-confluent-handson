# Step 1: リポジトリの取得と環境準備

```bash
git clone https://github.com/IBM/oic-i-agentic-ai-tutorials
cd oic-i-agentic-ai-tutorials/confluent-agents

python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

`requirements.txt` の内容:

```
confluent-kafka==2.14.0
python-dotenv==1.2.2
requests==2.32.5
fastmcp==3.1.1
```
