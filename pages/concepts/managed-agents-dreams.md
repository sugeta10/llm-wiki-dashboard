# Managed Agents Dreams（メモリ再構成パイプライン）

> **TL;DR**: 過去セッションとメモリストアを入力に、重複削除・矛盾解消・インサイト統合を非同期で行い、入力を壊さずに新しいメモリストアを生成する [[tools/claude-managed-agents]] のジョブ機能。

## 概念説明

長期運用ではエージェントのメモリが断片化・矛盾していき、蓄積されるほど質が落ちていく問題がある（[[papers/2026-peng-llm-memory-faulty]]）。Dreams が再構成を**入力非破壊**で行うのはこの問題への安全な対処であり、既存のメモリストアと過去のセッション記録（1〜100件）を入力に受け取り、結果を新しいストアとして別に書き出す。元の入力は一切変更されないため、出力品質をレビューして不要ならそのまま破棄し、再実行すればよい。

### 入力と出力

入力は2種類を混在できる。既存の `memory_store`（再構成対象のストア）と、生のやり取りである `sessions`（最大100件）。`instructions` パラメータでドリーム実行の重点領域を自然言語で指定できる（例: コーディングスタイルの好みに注目し、一回限りのデバッグメモは無視する）。`instructions` は最大4,096文字。

出力ストアIDは `outputs[]` に格納される。ジョブが `completed` した後は通常のメモリストアとして扱え、次セッションの `resources` に付与するだけで利用できる。入力は不変なので、出力品質が悪ければそのまま破棄して再実行すればよい。

### ライフサイクルと状態管理

ジョブは5状態をとる: `pending`（キュー済み）→ `running`（処理中）→ `completed` / `failed` / `canceled`。処理は非同期で、入力サイズに応じて時間がかかる。実行中は `session_id` フィールドでパイプラインのセッションを追跡でき、SSE ストリーミングでリアルタイムに観察できる。完了確認はポーリングで行う。

状態に応じた操作制約がある。`pending` / `running` 中の出力ストアの削除・アーカイブは 400 エラーで拒否される。入力ストアを途中で削除すると `input_memory_store_unavailable` でドリームが失敗する。エラー種別には `timeout` / `internal_error` / `memory_store_org_limit_exceeded` / `input_memory_store_too_large` / `input_memory_store_unavailable` / `input_session_unavailable` がある。

### 利用条件

Research Preview 段階でアクセスは申請制。`managed-agents-2026-04-01` と `dreaming-2026-04-21` の2つのベータヘッダーが必要（SDK は自動設定する）。課金は標準 API トークン料金で、入力セッション数・長さに比例する。

## API早見表

```python
# ドリーム作成
dream = client.beta.dreams.create(
    inputs=[
        {"type": "memory_store", "memory_store_id": store_id},
        {"type": "sessions", "session_ids": [session_a, session_b]},
    ],
    model="claude-opus-4-7",
    instructions="Focus on coding-style preferences; ignore one-off debugging notes.",
)

# 完了待ち（ポーリング）
while dream.status in ("pending", "running"):
    time.sleep(10)
    dream = client.beta.dreams.retrieve(dream.id)

# 出力ストアを次セッションに付与
output_store_id = next(
    o.memory_store_id for o in dream.outputs if o.type == "memory_store"
)
session = client.beta.sessions.create(
    agent=agent_id,
    resources=[{"type": "memory_store", "memory_store_id": output_store_id}],
)
```

## 検証済み事実

- 2026-05-23: Anthropic公式ドキュメント（platform.claude.com）に記載された正式仕様
- 2026-05-23: ライフサイクル5状態 — `pending`（キュー済み）→ `running`（処理中）→ `completed` / `failed` / `canceled`
- 2026-05-23: 出力ストアIDは `outputs[]` に格納。`completed` 後は通常のメモリストアとして利用可能（次セッションの `resources` に付与するだけ）
- 2026-05-23: 制限 — セッション数上限 100、`instructions` 上限 4,096文字
- 2026-05-23: `pending`/`running` 中の出力ストア削除・アーカイブは 400 エラーで拒否。入力ストア削除は `input_memory_store_unavailable` でドリーム失敗
- 2026-05-23: エラー種別 — `timeout` / `internal_error` / `memory_store_org_limit_exceeded` / `input_memory_store_too_large` / `input_memory_store_unavailable` / `input_session_unavailable`
- 2026-05-23: 必要ベータヘッダー — `managed-agents-2026-04-01` と `dreaming-2026-04-21`
- 2026-05-23: 対応モデルは `claude-opus-4-7` と `claude-sonnet-4-6`（ベータ期間中のみ）

## ベンダー横断の収束: 忘却でなく再合成

Claude の Dreams と同型の発想が ChatGPT 側にも現れている。OpenAI の新メモリ機能「**Dreaming**」は、長期メモリ運用の課題に「**消す**（忘却曲線で古い記憶を削除する）」のではなく「**裏で会話を読み直し、一貫した"今の自分"に書き直す**」=**再合成**で答えるという設計だと観察されている。これは Dreams の「重複削除・矛盾解消・インサイト統合を非破壊で行い、新しいメモリストアを生成する」という方向性と一致しており、自作の長期メモリを設計する側にとっては「忘却アルゴリズムを磨く」より「再合成パイプラインを持つ」方が筋がよい可能性を示唆する（[[concepts/agent-memory-layer]] / [[papers/2026-peng-llm-memory-faulty]] と接続）。

## 観察ログ（未検証）

- 2026-06-13 @conao_3: Fableを2日間使った中で「一番頼んで良かったのは記憶/MEMORYの再構成」だと述べる。自作の記憶システムでもClaude Codeビルトインの記憶ファイルでも同じように適用できるという主張。Dreamsが自動化する「再合成」を実践者が手動でも最高価値のタスクと評価した裏付け（単一ソース・個人の体験談。X bookmark 3,954・2026-07-01時点）
- 2026-06-05: @AI_masaou のポスト（X bookmark 293）。ChatGPTの新メモリ「Dreaming」は「古い記憶を忘却曲線で消す」のではなく「裏で会話を読み直して一貫した今の自分に書き直す＝忘却でなく再合成」という発想だと指摘。OpenAI公式の正式仕様ではなく個人の解釈・要約であり、機構の詳細は未確認
- 2026-05-23: Research Preview段階でアクセス申請制
- 2026-05-23: 処理時間は入力サイズによって「数分〜数十分」かかる（公式の概算）
- 2026-05-23: 課金は標準APIトークン料金。入力セッション数・長さに比例。小バッチから試して品質確認後にスケールアップ推奨

## 問い

- 出力ストアの品質を「破棄 / 採用」判定する基準を、ポーリング後に自動評価できないか試す
- `instructions` の指定粒度（重点領域の絞り方）でメモリ再構成の質がどれだけ変わるか小バッチで比較する

## 関連

- [[tools/claude-managed-agents]]（Dreams が属するプラットフォーム全体）
- [[papers/2026-peng-llm-memory-faulty]]（LLMエージェントのメモリ劣化問題 — Dreamsが解決しようとしている課題と対応）
- [[concepts/multi-agent-patterns]]（マルチエージェント環境でのメモリ共有設計）
- [[concepts/agent-memory-layer]]（複数エージェント間でメモリを共有する設計思想 — Dreamsはその「保持価値の選別」を担いうる）
- [[concepts/self-owned-ai-memory]]（個人が自作する長期記憶。忘却曲線＋「睡眠」工程を採る設計で、本ページの「忘却でなく再合成」と対照になる）
