# AI Employee Workflow

Claudeを完全自律動作する「AIスタッフ」として機能させるための**7日間セットアップ手法**。1日1テーマでシステムプロンプト設計→インターフェース選択→ワークフロー構築→コンテキスト強化→ツール接続→ルーティン拡張→改善ループを構築する。

関連: [[concepts/managed-agents-dreams]] / [[tools/claude-managed-agents]] / [[concepts/claude-best-practices]] / [[concepts/multi-agent-patterns]]

## 観察ログ（未検証）

- 2026-05-12: AIスタッフを「使う人」と「管理する人」の差はスキルでも課金プランでもなく**セットアップの有無**だと著者は主張
- 2026-05-12: **Day 1（役割定義）** — 対象領域は1つに絞る。完璧な業務デイ・自律判断の範囲・エスカレーション条件・品質基準を1ページで文書化→これがシステムプロンプトになる
- 2026-05-12: **Day 2（インターフェース選択）** — 非技術者は Claude Cowork（自律ファイル操作・スケジュールタスク）、開発者は Claude Code（ターミナル・API・MCP連携）を推奨
- 2026-05-12: **Day 3（ワークフロー設計）** — Trigger / Inputs / Process / Output の4要素でワークフローを定義し、1本だけ動かす
- 2026-05-12: **Day 4（メモリ・コンテキスト）** — Claude Coworkはセッション横断メモリ対応、Claude CodeはCLAUDE.mdで永続コンテキスト管理。「事業概要・品質基準・過去事例・ツール一覧・ルール」の5要素をコンテキスト文書化
- 2026-05-12: **Day 5（ツール接続）** — Gmail/GCal/GDrive/Slack/Notion/M365/GitHub/Linear コネクタでAIスタッフの行動範囲を拡大。各コネクタは能力を「乗算」すると著者は表現
- 2026-05-12: **Day 6（ルーティンスタック）** — 日次・週次・イベントトリガー・オンデマンドの4ワークフローを揃える目標。4本で週4〜10時間節約と著者は試算
- 2026-05-12: **Day 7（改善ループ）** — 毎週金曜に全ワークフローのアウトプット品質を1-10点評価し、弱い出力の原因を診断してプロンプトを更新する
- 2026-05-12: **5つのAIスタッフ原型** — Content Engine（コンテンツ研究・下書き）・Operations Manager（メール/ファイル/請求処理）・Code Reviewer（PR/バグ/ドキュメント）・Research Analyst（競合/市場/ニュース）・Customer Support Agent（チケットトリアージ/下書き）
- 2026-05-12: Managed AgentsのDreaming機能により、メタワークフロー（AIスタッフ自身が自分のアウトプットを評価し改善）をセッション間自動実行できると主張（→ [[concepts/managed-agents-dreams]]）

## 検証済み事実

- 2026-05-12: Claude Proは月$20、Claude Maxは月$100〜$200（Anthropic公式価格）
