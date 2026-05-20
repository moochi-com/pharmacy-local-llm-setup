# Advanced: DI業務向けマルチエージェント構成

> 対象: RAM 16GB 以上のPC / LM Studio または Python + Ollama を使用する方

複数のSLMモデルを同時に動かし、役割分担させることで、  
単一モデルより高品質なDI業務支援を実現できます。

---

## 推奨3エージェント構成

| エージェント | モデル | ファイルサイズ | 役割 |
|---|---|---|---|
| Agent A（推論） | LFM2.5-1.2B-Thinking | ~900MB | 推論・判断・根拠の論理的整理 |
| Agent B（長文） | Gemma4 E4B | ~5GB | 論文全文・長い文書の処理（128K活用） |
| Agent C（生成） | Bonsai-8B | 1.15GB | DI回答文の下書き・定型処理 |

**合計メモリ消費: 約7GB → RAM 16GBで余裕をもって同時運用可能**

---

## ワークフロー例：適応外使用問い合わせ

```
Step 1. Agent B (Gemma4 E4B)
  入力: 論文全文 + ガイドライン該当箇所
  処理: 関連エビデンスを要約・論点を抽出
    ↓
Step 2. Agent A (LFM2.5-1.2B-Thinking)
  入力: Agent B の要約結果
  処理: 推論プロセスを可視化しながら根拠を論理的に整理
        「この適応外使用を支持するエビデンスの強さは？」
        「リスク・ベネフィットのバランスは？」
    ↓
Step 3. Agent C (Bonsai-8B)
  入力: Agent A の整理結果
  処理: DI回答文の下書きを生成（実際に配布できる文体で）
```

---

## LM Studio での実装

### Step 1: 3モデルを順番にロード

LM Studio のモデル一覧から：
1. LFM2.5-1.2B-Thinking をロード → API サーバー起動（ポート 1234）
2. Gemma4 E4B をロード（同時ロード）
3. Bonsai-8B をロード（同時ロード）

### Step 2: Python から呼び出す

```python
from openai import OpenAI

# LM Studio のローカルAPI（全モデルが同一エンドポイントで利用可能）
client = OpenAI(base_url="http://localhost:1234/v1", api_key="not-needed")

# Step 1: Agent B で長文処理
def summarize_evidence(full_text: str) -> str:
    response = client.chat.completions.create(
        model="gemma4-e4b",  # LM Studio上のモデル名に合わせて変更
        messages=[
            {"role": "system", "content": (
                "あなたは医薬品情報の専門家です。"
                "提供された文書から、適応外使用に関するエビデンスを要約してください。"
                "エビデンスの質（RCT/症例報告等）と主要な結果を明示してください。"
            )},
            {"role": "user", "content": f"以下の文書を要約してください:\n\n{full_text}"}
        ],
        temperature=0.3,
        max_tokens=1000,
    )
    return response.choices[0].message.content

# Step 2: Agent A で推論・根拠整理
def analyze_reasoning(evidence_summary: str, drug_name: str, indication: str) -> str:
    response = client.chat.completions.create(
        model="lfm2.5-1.2b-thinking",  # LM Studio上のモデル名に合わせて変更
        messages=[
            {"role": "system", "content": (
                "あなたはDI業務専門の薬剤師です。"
                "ステップバイステップで考えてから、最終的な判断を述べてください。"
                "わからないことは「情報が不足している」と言ってください。"
            )},
            {"role": "user", "content": (
                f"薬剤: {drug_name}\n"
                f"適応外使用: {indication}\n"
                f"エビデンス要約:\n{evidence_summary}\n\n"
                "このエビデンスに基づき、適応外使用の根拠の強さと"
                "主なリスク・ベネフィットを整理してください。"
            )}
        ],
        temperature=0.4,
    )
    return response.choices[0].message.content

# Step 3: Agent C で回答文生成
def draft_di_response(analysis: str, drug_name: str) -> str:
    response = client.chat.completions.create(
        model="bonsai-8b",  # LM Studio上のモデル名に合わせて変更
        messages=[
            {"role": "system", "content": (
                "あなたは病院のDI担当薬剤師です。"
                "医師向けの簡潔なDI回答文を作成してください。"
                "必ず「この回答は参考情報であり、最終判断は担当医師が行ってください」と記載してください。"
            )},
            {"role": "user", "content": (
                f"以下の分析をもとに、{drug_name}の適応外使用についての回答文（下書き）を作成してください:\n\n{analysis}"
            )}
        ],
        temperature=0.5,
    )
    return response.choices[0].message.content

# 実行例
if __name__ == "__main__":
    paper_text = "（論文テキストをここに貼り付け）"
    drug = "○○薬"
    indication = "△△への適応外使用"

    print("=== Step 1: エビデンス要約 ===")
    summary = summarize_evidence(paper_text)
    print(summary)

    print("\n=== Step 2: 根拠・リスク分析 ===")
    analysis = analyze_reasoning(summary, drug, indication)
    print(analysis)

    print("\n=== Step 3: DI回答文（下書き） ===")
    draft = draft_di_response(analysis, drug)
    print(draft)
```

---

## 注意事項

- **必ず専門家が確認する**: AIの出力はあくまで下書きです。実際のDI回答は薬剤師が内容を確認・修正した上で使用してください
- **モデル名**: LM Studio内でのモデル表示名はダウンロード時のバリアントによって異なります。コード内のモデル名は実際の表示名に合わせてください
- **RAM**: Gemma4 E4B（~5GB）がメモリの大半を使用します。RAM 16GB 未満の場合は Gemma4 を Qwen2.5 3B（~2GB）に置き換えてください
