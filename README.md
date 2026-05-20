# pharmacy-local-llm-setup

ローカルLLM（大規模言語モデル）を病院・薬局の業務PCで動かすためのセットアップガイドです。  
薬剤師・医療従事者向けに、プライバシーを守りながらAIを活用できる環境を構築します。

**本リポジトリは個人活動として公開しています。所属組織とは無関係です。**  
**本ツールは医療行為の代替ではありません。AIの出力は必ず専門家が確認してください。**

---

## なぜローカルLLMか

| 課題 | ローカルLLMによる解決 |
|---|---|
| クラウドAIに患者情報を送れない | データが院内PCから出ない |
| 月額サブスクが重い | 電気代のみで稼働（ほぼ0円） |
| ネット接続が不安定・制限される | オフライン完全動作 |
| DI FAQ・文書整理など定型業務に使いたい | 軽量SLMで十分実用的 |

---

## セットアップ方法を選ぶ

### ルートA：Ollama + Open WebUI（Docker）

複数PCからブラウザで共有利用したい場合・API連携が必要な場合に最適。

→ [guides/quickstart.md](guides/quickstart.md)

```
docker compose up -d
# → http://localhost:3000 でアクセス
```

### ルートB：LM Studio（GUIアプリ）

個人PCで手軽に始めたい場合・Dockerなしで動かしたい場合に最適。  
LFM2.5 / Bonsai-8B / Gemma4 E4B はUIから直接ダウンロード可能。

→ [guides/lmstudio_quickstart.md](guides/lmstudio_quickstart.md)

---

## 推奨モデル早見表（2026年5月時点）

| モデル | サイズ | GPU不要 | 日本語 | 用途 |
|---|---|---|---|---|
| ★ LFM2.5-1.2B-Thinking | ~900MB | ◎ | ○ | 推論・根拠整理・DI判断 |
| ★ LFM2.5-1.2B-Instruct | ~850MB | ◎ | ○（JP版◎） | 定型処理・高速応答 |
| ★ Bonsai-8B | 1.15GB | ◎ | ○ | 8B品質・低RAM |
| ★ Gemma4 E4B | ~5GB | △ | ○ | 論文全文・128Kコンテキスト |
| Qwen2.5 3B | ~2GB | ○ | ◎ | 日本語最高品質・汎用 |
| Phi-4 mini | ~2.5GB | ○ | ○ | 論文批判的吟味・構造化出力 |

★ = 作者が薬剤師業務で実際に使用・有効性確認済み  
→ 詳細：[models/recommended_models.md](models/recommended_models.md)

---

## RAM別 推奨構成

| RAM | 推奨構成 |
|---|---|
| 4GB以下 | LFM2.5-1.2B-Thinking または Instruct のみ |
| 8GB | LFM2.5-1.2B + Bonsai-8B（合計 ~2.2GB） |
| 16GB | LFM2.5-1.2B-Thinking + Gemma4 E4B + Bonsai-8B（マルチエージェント構成） |
| 32GB以上 | 全モデル常時ロード |

---

## ドキュメント一覧

| ファイル | 内容 |
|---|---|
| [guides/quickstart.md](guides/quickstart.md) | Docker + Ollama + Open WebUI セットアップ |
| [guides/lmstudio_quickstart.md](guides/lmstudio_quickstart.md) | LM Studio（GUI）セットアップ |
| [guides/hospital_security.md](guides/hospital_security.md) | 院内運用のセキュリティ考慮事項 |
| [guides/multi_agent_di.md](guides/multi_agent_di.md) | Advanced: DI業務向けマルチエージェント構成 |
| [models/recommended_models.md](models/recommended_models.md) | モデル詳細・実使用レポート |
| [models/model_comparison.md](models/model_comparison.md) | 用途別・コンテキスト長比較表 |
| [configs/modelfile_examples/](configs/modelfile_examples/) | Ollama Modelfileサンプル |

---

## プロンプト集との連携

本リポジトリでセットアップした環境で使えるプロンプト集：  
→ [pharmacy-prompt-library](https://github.com/moochi-com/pharmacy-prompt-library)

---

## 動作確認済み環境

- OS: Windows 11 Home / macOS Sonoma 14
- Docker Desktop 4.x
- LM Studio 0.3.x
- Ollama 0.4.x / Open WebUI latest
- 確認日: 2026-05-20

---

## ライセンス

MIT License

---

*このリポジトリは個人の薬剤師が業務効率化のために公開しているものです。医療判断には必ず一次情報（添付文書・ガイドライン等）を確認してください。*

---

## Why Local LLM for Healthcare?

This repository provides setup guides for running Large Language Models (LLMs) locally on hospital/pharmacy PCs — without sending any data to the cloud.

**Key benefits for healthcare professionals:**
- Patient data never leaves your local machine
- Works offline (no internet dependency)
- Near-zero running cost (electricity only)
- Practical for routine tasks: DI FAQ, document drafting, literature summarization

**Disclaimer:** This is a personal project, unaffiliated with any organization. AI outputs must always be verified by qualified professionals. This tool does not replace medical judgment.
