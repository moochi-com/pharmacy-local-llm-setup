# クイックスタート：Ollama + Open WebUI（Docker）

Docker を使ってブラウザからローカルLLMを使えるようにします。  
複数PCからの共有利用・API連携が必要な場合に最適です。

---

## 必要なもの

- Docker Desktop（[docker.com](https://www.docker.com/products/docker-desktop/) からインストール）
- RAM 8GB 以上推奨（4GBでも軽量モデルは動作可）
- 空きストレージ 10GB 以上

---

## Step 1: Docker Desktop を起動

インストール後、Docker Desktop を起動してください。  
タスクバーのDockerアイコンが緑色になれば準備完了です。

---

## Step 2: docker-compose.yml を配置

本リポジトリのルートにある `docker-compose.yml` をそのまま使います。

```bash
# リポジトリをクローンする場合
git clone https://github.com/moochi-com/pharmacy-local-llm-setup.git
cd pharmacy-local-llm-setup
```

または `docker-compose.yml` だけダウンロードして任意のフォルダに置いてください。

---

## Step 3: コンテナを起動

```bash
docker compose up -d
```

初回は Open WebUI のイメージダウンロードに数分かかります。

---

## Step 4: ブラウザでアクセス

```
http://localhost:3000
```

初回アクセス時にアカウント登録を求められます（ローカルのみ有効・外部に送信されません）。

---

## Step 5: モデルをダウンロード

Open WebUI の設定画面、または Ollama CLI でモデルを取得します。

```bash
# Ollama CLI での取得（Docker内のコンテナに対して実行）
docker exec ollama ollama pull qwen2.5:3b

# 日本語DI業務向け推奨
docker exec ollama ollama pull qwen2.5:3b   # 最も簡単・日本語◎
docker exec ollama ollama pull phi4-mini    # 論文批判的吟味に強い
docker exec ollama ollama pull gemma4:4b    # 128K・論文全文処理
```

LFM2.5 シリーズ・Bonsai-8B は HuggingFace からの手動インポートが必要です。  
→ **LM Studio を使うと GUI から簡単にダウンロードできます。** [lmstudio_quickstart.md](lmstudio_quickstart.md) 参照。

---

## 動作確認

```bash
# Ollama API が応答するか確認
curl http://localhost:11434/api/tags
```

または Open WebUI のチャット画面でモデルを選択して動作確認してください。

---

## コンテナの停止・再起動

```bash
# 停止
docker compose stop

# 再起動
docker compose start

# 削除（データは volumes に保持される）
docker compose down
```

---

## セキュリティ設定について

`docker-compose.yml` のポートバインドは `127.0.0.1:11434:11434` と `127.0.0.1:3000:8080` に設定済みです。  
これにより **院内LANからのアクセスを遮断**し、localhost のみに限定しています。

院内共有での利用方法・追加のセキュリティ設定は [hospital_security.md](hospital_security.md) を参照してください。
