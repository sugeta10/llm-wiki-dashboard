# Cursorの5つのAI指示方法（AGENTS.md / Rules / Commands / Skills / Subagents）

> **TL;DR**: AIエージェントへの指示は「常時自動か手動起動か」「プロジェクト固有か再利用可能か」「コンテキストを共有するか分離するか」の3軸で5つの手段に整理でき、軸の組み合わせで使う道具が一意に決まる。

同じ「AIに指示を与える」でも、適用タイミング・再利用範囲・コンテキスト分離の3軸で要件が異なると、最適な格納先が変わる。この記事の本質は5機能のスペック説明ではなく、**この3軸で要件を分解すれば道具選びが機械的に決まる**という判断フレームにある。軸を意識せず全部 `AGENTS.md` に書くと条件付き適用や再利用が効かず、逆に何でもスキル化すると自動判断のトリガーが濁る。

| 手段 | 適用タイミング | 再利用範囲 | コンテキスト | 一言で |
|------|------------|----------|-----------|-------|
| **AGENTS.md** | 常に自動適用 | プロジェクト限定 | 親と共有 | プロジェクトのREADME的な基本方針 |
| **Rules**（`.cursor/rules/*.mdc`） | 常時/特定ファイル/AI判断/手動 | プロジェクト限定 | 親と共有 | 条件付き・柔軟な継続指示 |
| **Commands**（`.cursor/commands/*.md`） | 手動起動のみ `/名前` | プロジェクト限定 | 親と共有 | ボタン一つの定型ワークフロー |
| **Skills**（`.cursor/skills/*/SKILL.md`） | AIが自動判断 or 手動 | 複数プロジェクト可 | 親と共有 | ポータブルな専門知識パック |
| **Subagents**（`.cursor/agents/*.md`） | AIが自動判断 or 手動 | 複数プロジェクト可 | **独立コンテキスト** | タスク委譲・並列処理 |

## 3軸で選ぶ判断ロジック

- **自動か手動か** — 常に効かせたい背景指示なら `AGENTS.md`/`Rules`、明示的に呼びたい定型作業なら `Commands`、AIに使いどころを判断させたいなら `Skills`/`Subagents`。
- **プロジェクト固有か汎用か** — 自社固有の業務ルール（決済フロー・承認段階）は `Rules`、業界標準のベストプラクティス（金融計算でDecimal型を使う等）は `Skills`。「他プロジェクトでも使えるか」が分岐点。
- **コンテキストを分けるか** — 長時間リサーチや並列ワークストリームでメインの会話を汚したくないときだけ `Subagents`。それ以外は親コンテキスト共有で十分。

`AGENTS.md` と `Rules` は連続体で、シンプルな全体方針で足りるなら前者、ディレクトリ限定（`globs`）・`alwaysApply` 制御・`@filename` 参照といった柔軟性が要るなら後者へ昇格する。

## SkillsとSubagentsの境界

両者ともAI自動判断・再利用可能だが、**コンテキスト分離の要否**で割れる。単一目的で一度の実行で完結し別コンテキストが不要なら Skills、多段ステップ・長時間・並列・独立検証が要るなら Subagents。Subagentは各自が独立コンテキストウィンドウを持つためトークンを個別消費し（5並列なら約5倍）、起動オーバーヘッドとレイテンシのトレードオフがある。この設計判断は [[tools/claude-code-subagents]] や [[concepts/multi-agent-patterns]] と同じ論点で、Claude Codeでも構図は変わらない。

Skillsの中核である段階的開示（Progressive Disclosure）—Level1メタデータ常時ロード→Level2 SKILL.md全文→Level3参照リソース—の詳細は [[concepts/claude-skills]] を参照。AGENTS.mdはオープン標準として複数ツールで共通化が進んでおり、Cursorに限らず [[tools/cursor]] / Claude Code 双方で同じ思想が通底する。

## 観察ログ（未検証）

- 2026-06-04: Anthropicが2025年10月にAgent Skillsをオープン標準として発表し、Claude Code・Cursor等複数ツールで導入された、という整理（記事は2026年1月時点情報を基にすると明記）
- 2026-06-04: Subagentアンチパターンとして「汎用サブエージェントを50個以上作らない」「あいまいな説明（"一般的なタスクに使用"）を避け『OAuthプロバイダ認証実装時に使用』のように具体化」「2000語の長すぎるプロンプトは賢くせず遅く保守しづらくするだけ」「まず2〜3個から始める」を提示（著者の整理）
- 2026-06-04: 実例として Vercel の React Best Practices スキル（40以上のルールを8カテゴリ・影響度CRITICAL〜LOWで優先度付け、`npx add-skill vercel-labs/agent-skills` で導入）を紹介
- 2026-06-19: Claude Code版として、振る舞いカスタマイズの「7つの手段」を ①いつコンテキストに読み込まれるか ②長いセッションでどう振る舞うか ③どれくらい指示の従いやすさを持つか の3軸で整理した初心者向け記事が紹介された（@oikon48）。Cursorの5手段×3軸（適用タイミング／再利用範囲／コンテキスト分離）と並行する構図だが、軸の取り方が異なる（こちらはコンテキストの読込タイミング・長期セッションでの挙動・指示遵守度に重心）。具体的な7手段の中身は紹介ポストがteaserのため元記事未取得

## 問い

- このCursorの5分類は、自分のLLM Wikiの運用（CLAUDE.md直書きの操作ルール vs `.claude/skills/` のスキル化）にそのまま当てはめられるか。どこを Rules 相当、どこを Skills 相当に振るべきか
- 「自動判断 vs 手動起動」の境界で、AIのトリガー精度が落ちる手段（Skills/Subagents）はどの程度信頼できるか。手動 `/名前` 起動と併用すべきか
- [[concepts/claude-code-context-hierarchy]] の4層（Enterprise→Global→Project→local）と、この「適用タイミング×再利用範囲」軸は直交するのか、重なるのか

## 関連

- [[concepts/claude-skills]] — Skillsの段階的開示・永続的職務定義の詳細（同概念のClaude側）
- [[tools/claude-code-subagents]] — 独立コンテキスト・フォーク起動・built-inサブエージェント（Subagentsの実装側）
- [[tools/cursor]] — AI統合IDE本体
- [[concepts/multi-agent-patterns]] — Subagent並列処理の設計パターン全般
- [[concepts/claude-code-context-hierarchy]] — 設定の4層構造（別軸の整理）
- [[concepts/agents-md-canonical]] — AGENTS.md を正本に Claude Code/Codex で設定を共通化する具体パターン（指示文 vs 実行機構の線引き）
- [[concepts/claude-code-instruction-methods]] — Claude Code版の7手段フレーム（観察ログで予告していた元記事）。軸が「ロード時点×圧縮生存×権威」に寄る
