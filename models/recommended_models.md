# 推奨SLMモデル一覧

> 最終更新: 2026-05-20  
> ★ = 作者が薬剤師業務（DI業務・文献調査・文書作成）で実際に使用・有効性確認済み

---

## ★ 実使用実績モデル

### LFM2.5-1.2B-Thinking（Liquid AI）★ 最推奨

- パラメータ: 1.2B
- ファイルサイズ: ~900MB（条件次第で720MB）
- 日本語: ○
- GPU: 不要（◎ CPU完全動作）
- MATH-500スコア: 88（Instruct版から大幅向上）

**特徴：** 回答前に内部で思考トレース（なぜそう判断するか）を生成してから最終回答を出す。推論が必要なタスクで真価を発揮。

**薬剤師業務での用途：**
- 薬物相互作用の機序を段階的に推論
- 複数エビデンスを比較・統合するDI回答
- 適応外使用の根拠の論理的整理
- 研究デザインの批判的吟味

**Ollamaでの利用：** HuggingFaceからGGUF取得してインポート  
**LM Studio：** ネイティブサポート（初日から対応）

> 注意: 日常チャット・文書生成は Instruct 版が適している。

---

### LFM2.5-1.2B-Instruct（Liquid AI）★

- パラメータ: 1.2B
- ファイルサイズ: ~850MB（GGUF Q4_0）
- 日本語: ○（JP変種は◎）
- GPU: 不要（◎ CPU完全動作）
- JMMLUスコア: JP版 50.7 / Instruct版 47.7

**薬剤師業務での用途：**
- DI問い合わせの定型回答下書き
- 副作用説明文の初稿作成
- 配合変化・用量確認などの短文処理
- マルチエージェント構成での高速処理担当

**Ollamaでの利用：** `ollama pull hf.co/liquid-ai/LFM2.5-1.2B-Instruct-GGUF`  
**LM Studio：** UIから直接ダウンロード可

---

### Bonsai-8B（PrismML）★

- パラメータ: 8B（実質）
- ファイルサイズ: 1.15GB（1ビット量子化）
- 日本語: ○
- GPU: 不要（◎ ~2GB RAM以下で動作）

**特徴：** 1ビット量子化技術により8Bパラメータが1.15GBで動作。通常8Bモデルは4〜8GBのRAMが必要なため驚異的な圧縮率。

**薬剤師業務での用途：**
- RAM 8GB以下の低スペックPCでの高品質推論
- 複数ステップの推論が必要なDI問い合わせ
- 適応外使用の根拠整理など論理的判断を要するタスク

**Ollamaでの利用：** PrismML公式手順参照（1ビット特殊形式のため通常手順と異なる）  
**LM Studio：** UIから直接ダウンロード可

> **コミュニティ評価について：** 「hype寄り」「Gemma4 E4Bより下」との意見もある。サイズ・速度重視の場面に限定推奨。複雑な推論は LFM2.5-Thinking を優先。

---

### Gemma4 E4B（Google DeepMind）★

- パラメータ: 4B（有効パラメータ）
- ファイルサイズ: ~5GB（Q4_0）/ ~7.5GB（SFP8）
- コンテキストウィンドウ: **128Kトークン**（最大の強み）
- 日本語: ○（多言語学習）
- GPU: △（Q4_0で5GB RAM必要。RAM 16GB以上推奨）

**薬剤師業務での用途：**
- 論文全文の一括要約（フルテキスト投入可）
- 複数添付文書を比較参照した回答生成
- レジメン全体を踏まえた副作用管理相談
- 長い診療ガイドラインの横断検索

**Ollamaでの利用：** `ollama pull gemma4:4b`  
**LM Studio：** UIから直接ダウンロード可

---

## 定番SLMモデル

### Qwen2.5 3B（Alibaba Cloud）

- パラメータ: 3B / ファイルサイズ: ~2GB
- 日本語: ◎（最高クラス）/ GPU: ○ CPU動作可
- **Ollama最も簡単：** `ollama pull qwen2.5:3b`
- 用途: DI問い合わせ回答下書き・日本語文献要約・副作用説明文

### Phi-4 mini（Microsoft）

- パラメータ: 3.8B / ファイルサイズ: ~2.5GB
- 日本語: ○ / GPU: ○ CPU動作可 / 医療テキスト: ◎
- **Ollama：** `ollama pull phi4-mini`
- 用途: 論文の批判的吟味・薬物相互作用説明・研究デザイン確認・英語論文の日本語要約

### ELYZA-JP 7B（ELYZA）

- パラメータ: 7B / ファイルサイズ: ~4GB
- 日本語: ◎（最高水準）/ GPU: △ GPU推奨 / RAM: 16GB必須
- **Ollama：** `ollama pull elyza:jp7b`
- 用途: 高品質な日本語文書生成・患者向け説明文・和文医学論文要約

---

## RAM別 推奨構成まとめ

| RAM | 推奨構成 |
|---|---|
| 4GB以下 | LFM2.5-1.2B-Thinking または Instruct のみ |
| 8GB | LFM2.5-1.2B + Bonsai-8B（合計 ~2.2GB） |
| 16GB | LFM2.5-1.2B-Thinking + Gemma4 E4B + Bonsai-8B（マルチエージェント） |
| 32GB以上 | 全モデル常時ロード・ELYZA-JP 7B も快適動作 |

---

## 参考リンク

- [Liquid AI LFM2.5](https://www.liquid.ai/ja/blog/introducing-lfm2-5-the-next-generation-of-on-device-ai)
- [LFM2.5-1.2B-Thinking](https://www.liquid.ai/ja/blog/lfm2-5-1-2b-thinking-on-device-reasoning-under-1gb)
- [Gemma4 ドキュメント](https://ai.google.dev/gemma/docs/core)
- [PrismML Bonsai-8B](https://ledge.ai/articles/prismml_bonsai_8b_1bit_llm_1_15gb_edge_ai)
