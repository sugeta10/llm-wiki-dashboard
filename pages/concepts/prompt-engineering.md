# プロンプトエンジニアリング

LLMへの入力（プロンプト）を設計・最適化する技術。適切なプロンプト設計により出力品質を大幅に向上させられる。

## 主要テクニック

### チェーンプロンプト（Chain Prompting）
複数のプロンプトを連鎖させ、複雑なタスクを段階的に処理する手法。

### Few-shot プロンプティング
回答例をプロンプト内に含めることでモデルの出力フォーマットを誘導する。

### ハルシネーション対策
- プロジェクトの詳細コンテキストを冒頭に提供する
- 「知らない場合は知らないと答えよ」と明示する
- [[tools/cursor]] では詳細コンテキスト記述でハルシネーション85%削減の報告あり

## Claude公式プロンプト講座

[[companies/anthropic]] が公開した初心者向けプロンプト講座では以下を学べる：
- 基礎的なプロンプト作成方法
- 実際のユースケース
- チェーンプロンプト・Few-shot・ハルシネーション回避法
- インタラクティブな実習形式

## ロールプロンプティング

ChatGPT o1 Pro に「超次元生命体であるAIとして超論理的・超俯瞰的に考察せよ」と付加することで応答品質が向上するという報告がある（効果の再現性は個人差あり）。

## GPT-5.5向けプロンプト設計（OpenAI公式ガイド）

[[models/gpt-5-5]] では「アウトカムファースト」へのパラダイムシフトが推奨される。

### 旧来スタイルとの違い

- **旧**: 「まずAを確認し、次にBと比較し、すべての例外を検討し…」（手順を細かく指定）
- **新**: 「問題を解決せよ。成功条件はX、制約はY」（目的・成功条件・制約のみ渡す）

旧モデルはステイ・オン・トラックのために手順指定が必要だったが、GPT-5.5では過剰指定が探索空間を狭め機械的な回答を招く。

### 検索バジェット（Retrieval Budget）

> 「最小限の証拠で正確に答え、正確に引用し、停止する」

RAGシステムで検索回数を明示的に制御する停止ルール。追加検索を許可するのは「必要な事実が欠けている場合のみ」。文言改善・例示追加目的の再検索は不要。

### ABS（ALWAYS / NEVER）の使い方を絞る

旧来のプロンプトは `ALWAYS`・`NEVER`・`must`・`only` を多用していたが、GPT-5.5では真の不変条件（安全ルール、必須出力フィールド）にのみ使用し、判断系は**decision rules**（条件分岐的な記述）で代替する。

## Claude 4.x モデル向けプロンプト設計（Anthropic公式）

[[models/claude-opus-4-7]]・[[tools/claude-managed-agents]] 等のClaude 4.x系モデルに特有の動作と調整指針。

### effortパラメータ

Claude 4.x系ではtradeoffを `effort` で制御する（`low` / `medium` / `high` / `xhigh` / `max`）。

| effortレベル | 推奨用途 |
|------------|---------|
| `xhigh`（新） | コーディング・エージェント用途のデフォルト |
| `high` | 知識集約型タスクの最低ライン |
| `medium` | コスト重視でトークン削減が必要な場合 |
| `low` | レイテンシ敏感・非知識集約タスク |

### Adaptive Thinking（適応的思考）

Claude 4.6以降はデフォルトで adaptive thinking（`thinking: {type: "adaptive"}`）を使用。`budget_tokens` による手動指定は非推奨。effortパラメータで思考深度を制御する。

```python
client.messages.create(
    model="claude-opus-4-7",
    max_tokens=64000,
    thinking={"type": "adaptive"},
    output_config={"effort": "xhigh"},
    messages=[{"role": "user", "content": "..."}],
)
```

### Claude Opus 4.7 固有の動作変化

- **より文字通りに解釈する**: 指示の適用範囲を明示しないと、最初の項目にしか適用されない
- **verbosity自己調整**: 複雑さに応じて応答長を自動調整（固定のverbosityに依存するプロダクトは要調整）
- **ツール使用量が少ない傾向**: 推論を優先する。ツールを増やしたい場合は effort を上げるか明示的に指示する
- **サブエージェント生成が少ない傾向**: 明示的に「いつサブエージェントを使うか」を指示する
- **よりダイレクトで意見がある文体**: warmな応答が必要なら「親しみやすい協調的な口調で」のように明示する

### 並列ツール呼び出し促進プロンプト

```
<use_parallel_tool_calls>
独立したツール呼び出しは並列で実行してください。例えば3つのファイルを読む場合は同時に3回呼び出す。ただし後の呼び出しが前の結果に依存する場合は順次実行すること。
</use_parallel_tool_calls>
```

### 長期エージェントタスクのコンテキスト管理

複数のコンテキストウィンドウにまたがるタスク：
- 最初のウィンドウでフレームワーク（テスト・セットアップスクリプト）を作成
- 以降のウィンドウではtodo-listを使って反復
- コンテキストが切れる前に状態をファイル/メモリに保存

### Prefill（応答先読み）の廃止

Claude 4.6以降、アシスタントターンへの先頭埋め込み（prefill）は非推奨。明示的な指示で代替する。

## Claude サブスク活用プロンプト20選

@shota7180 が海外で話題になった実用プロンプト集を日本語でまとめている（元ネタ: @AnatoliKopadze の X記事）。

> 多くの人はサブスク料金の10%ほどしか活かせていない。重要なのは「プロンプトの質」。

月20ドル（$20）のClaudeを自分専用アシスタントに変えるための20個の実用プロンプトが紹介されている。詳細は [[sources/twitter/2026-05-16/2055602484853567963.md]] および元記事を参照。

## 関連

- [[concepts/claude-best-practices]] — Claude効果的活用18ステップ（Projects・Custom Instructions・Extended Thinking・スタイルクローニング）
- [[companies/anthropic]]
- [[models/claude-opus-4-7]]
- [[tools/cursor]]
- [[concepts/vibe-coding]]
- [[concepts/llm-personality-injection]] — MBTI等の性格モデルをシステムプロンプトで注入してLLMの振る舞いを変える手法
- [[concepts/self-refining-skills]]
- [[models/gpt-5-5]]（GPT-5.5向けプロンプティング詳細）
- [[concepts/workload-identity-federation]]
- [[tools/dspy]] — プロンプトエンジニアリングを最適化問題に変換するフレームワーク
- [[concepts/prompt-cache-prewarm]] — システムプロンプト事前送信でtime-to-first-tokenを短縮するキャッシュウォームアップ手法
- [[tools/openai-prompt-optimizer]] — OpenAIのプロンプト自動最適化ツール（メタプロンプティングのUI実装）
- [[concepts/implementation-notes-prompt]] — 実装時に並走ノートを書かせるプロンプト手法（Anthropicエンジニア発）
- [[concepts/chatgpt-custom-instructions]] — ChatGPTカスタム指示の設定手法（全チャット共通の永続プロンプト設定）
- [[concepts/prompt-engineering-playbook]] — 海外AIガチ勢の実践プロンプト40技術（CoVe・ToT・マルチエージェント・ハーネス設計）
