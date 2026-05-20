# クイックスタート：LM Studio（GUIアプリ）

Docker なしで、GUI アプリだけでローカルLLMを動かす方法です。  
個人PCで手軽に始めたい方に最適です。

---

## LM Studio とは

- Windows / Mac / Linux 対応のデスクトップアプリ
- モデルの検索・ダウンロード・チャットがすべて GUI で完結
- OpenAI 互換のローカルAPI サーバーも内蔵
- **LFM2.5-1.2B / Bonsai-8B / Gemma4 E4B** はUIから直接ダウンロード可

ダウンロード: [https://lmstudio.ai/](https://lmstudio.ai/)

---

## Step 1: LM Studio をインストール

1. 上記リンクから OS に合ったインストーラーをダウンロード
2. インストール後、アプリを起動

---

## Step 2: モデルをダウンロード

左サイドバーの「Models」（虫眼鏡アイコン）をクリック。

**推奨モデルの検索・ダウンロード手順：**

```
LFM2.5-1.2B-Thinking  → 検索: "LFM2.5 Thinking" → Liquid AI の GGUF を選択
LFM2.5-1.2B-Instruct  → 検索: "LFM2.5 Instruct" → JP版を推奨
Bonsai-8B              → 検索: "Bonsai" → PrismML のモデルを選択
Gemma4 E4B             → 検索: "Gemma4 E4B" → Q4_K_M を推奨（RAM 8GB以上）
Qwen2.5 3B             → 検索: "Qwen2.5 3B" → Instruct 版を選択
```

**量子化の選び方：**

| 量子化 | RAM消費 | 品質 | 推奨場面 |
|---|---|---|---|
| Q4_K_M | 少 | 中〜高 | バランス重視（通常はこれ） |
| Q5_K_M | 中 | 高 | RAM に余裕がある場合 |
| Q8_0 | 多 | 最高 | RAM 16GB 以上 |

---

## Step 3: チャットで動作確認

1. 上部のモデル選択欄からダウンロード済みモデルを選択
2. 「New Chat」でチャット開始
3. 試しに「添付文書のポイントを3つにまとめてください」と入力してみる

---

## Step 4（任意）: ローカルAPIサーバーを起動

左サイドバーの「Local Server」（サーバーアイコン）から起動できます。

```
http://localhost:1234/v1
```

Python や他のアプリから OpenAI 互換の API として利用できます。

```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:1234/v1", api_key="not-needed")

response = client.chat.completions.create(
    model="lmstudio-community/LFM2.5-1.2B-Instruct-GGUF",
    messages=[{"role": "user", "content": "この薬の禁忌を教えてください"}],
    temperature=0.5,
)
print(response.choices[0].message.content)
```

---

## マルチエージェント構成（RAM 16GB以上）

LM Studio は複数モデルを同時ロードできます。  
DI業務向けの3エージェント構成例：

```
Agent A: LFM2.5-1.2B-Thinking  (~900MB)   → 推論・判断
Agent B: Gemma4 E4B            (~5GB)     → 長文文書処理
Agent C: Bonsai-8B             (1.15GB)   → 定型処理・高速応答
合計: ~7GB → RAM 16GB で余裕をもって動作
```

→ 詳細は [multi_agent_di.md](multi_agent_di.md) を参照

---

## 注意点

- **モデルファイルの保存先**：デフォルトでは `C:\Users\<ユーザー名>\.lmstudio\models\`（Windowsの場合）
- **ストレージ**：モデルによって1〜7GB 程度の空き容量が必要
- **院内での利用**：データは外部に出ません。ただしLM Studioアプリの更新確認はインターネット接続が発生します
