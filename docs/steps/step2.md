# Step 2: Confluent Cloud で Kafka クラスターと API キーを作成

1. Confluent Cloud にログインし、Environment を作成（または既存のものを選択）
2. Kafka クラスター（Basic で可）を作成
3. **Cluster settings → Endpoints** から **Bootstrap server** を控える  
   （形式: `pkc-xxxxx.<region>.<provider>.confluent.cloud:9092`）
4. **API Keys → Add key**（クラスター用）を作成し、**Key / Secret** を控える
