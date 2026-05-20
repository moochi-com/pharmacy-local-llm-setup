# モデル用途別比較表

> 最終更新: 2026-05-20

---

## 用途別 最適モデル早見表

| 用途 | 最適モデル | 理由 |
|---|---|---|
| DI問い合わせ 定型回答下書き | LFM2.5-1.2B-Instruct / Qwen2.5 3B | 高速・日本語品質 |
| 薬物相互作用 機序の推論 | LFM2.5-1.2B-Thinking | 推論トレース可視化 |
| 論文全文要約（長文） | Gemma4 E4B | 128Kコンテキスト |
| 英語論文 → 日本語要約 | Phi-4 mini / Gemma4 E4B | 医療英語理解度 |
| 適応外使用 根拠整理 | LFM2.5-1.2B-Thinking | 段階的推論が必要 |
| 患者向け説明文 生成 | ELYZA-JP 7B / Qwen2.5 3B | 日本語自然さ |
| 院内周知文 草案作成 | LFM2.5-1.2B-Instruct | 定型文・高速 |
| 研究デザイン批判的吟味 | Phi-4 mini / LFM2.5-1.2B-Thinking | 論理推論・構造化出力 |
| 低スペックPC（RAM 8GB以下） | LFM2.5-1.2B-Thinking + Bonsai-8B | 合計 ~2.2GB |

---

## コンテキストウィンドウ比較

| モデル | コンテキスト長 | 長文処理の目安 |
|---|---|---|
| Gemma4 E4B | **128K** | 論文全文・複数添付文書同時処理 ◎ |
| Qwen2.5 3B | ~32K | ガイドライン1章・中程度の文書 ○ |
| LFM2.5-1.2B | ~32K | 添付文書1本・中程度の文書 ○ |
| Phi-4 mini | ~128K | 論文全文・アブストラクト群 ○ |
| Bonsai-8B | ~4K〜8K | 短〜中文書（DI回答・定型処理） △ |
| ELYZA-JP 7B | ~4K〜8K | 短文中心 △ |

---

## ファイルサイズ・スペック比較

| モデル | パラメータ | ファイルサイズ | GPU必要 | RAM目安 |
|---|---|---|---|---|
| LFM2.5-1.2B-Thinking | 1.2B | ~900MB | 不要 | 2GB |
| LFM2.5-1.2B-Instruct | 1.2B | ~850MB | 不要 | 2GB |
| Bonsai-8B | 8B（1bit） | 1.15GB | 不要 | 2GB |
| Qwen2.5 3B | 3B | ~2GB | 不要 | 4GB |
| Phi-4 mini | 3.8B | ~2.5GB | 不要 | 4GB |
| Gemma4 E4B | 4B | ~5GB（Q4） | 推奨 | 8GB以上 |
| ELYZA-JP 7B | 7B | ~4GB | 推奨 | 8GB以上 |

---

## ツール別 対応状況

| モデル | Ollama | LM Studio | 備考 |
|---|---|---|---|
| LFM2.5-1.2B-Thinking | HF経由インポート | ◎ ネイティブ対応 | LM Studioが簡単 |
| LFM2.5-1.2B-Instruct | HF経由インポート | ◎ UIからDL | JP版はLM Studio推奨 |
| Bonsai-8B | 公式手順参照 | ◎ UIからDL | 1bit特殊形式 |
| Gemma4 E4B | `ollama pull gemma4:4b` | ◎ UIからDL | どちらも容易 |
| Qwen2.5 3B | `ollama pull qwen2.5:3b` | ◎ UIからDL | Ollama最も簡単 |
| Phi-4 mini | `ollama pull phi4-mini` | ◎ UIからDL | どちらも容易 |
| ELYZA-JP 7B | `ollama pull elyza:jp7b` | ◎ UIからDL | どちらも容易 |

---

## セットアップツール比較

| ツール | 難易度 | 向いている用途 |
|---|---|---|
| **Ollama + Open WebUI（Docker）** | 中 | 複数PCからの共有利用・API連携・薬剤部サーバー設置 |
| **LM Studio（GUIアプリ）** | 低 | 個人PC・複数モデル同時起動・マルチエージェント開発 |
| llama.cpp | 中〜高 | CPU効率最大化・組み込み用途 |
| LocalAI | 中 | DockerでOpenAI互換APIを提供 |

→ **初めての方には LM Studio（ルートB）を推奨します。**
