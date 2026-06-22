# Claude Code 公式プラグイン一覧

[[tools/claude-code]] の公式マーケットプレイス `claude-plugins-official` に掲載されている全 **123個のプラグイン**（2026年4月5日時点）の解説。

## インストール

```text
/plugin install <プラグイン名>@claude-plugins-official
```

## 提供者区分

| 表記 | 説明 |
|------|------|
| 🔵 Anthropic | Anthropic が開発・配布 |
| 🟡 Third party | Anthropic 管理の外部プラグイン・各社公式プラグイン |

## プラグインタイプ

| タイプ | 説明 |
|--------|------|
| MCP | Model Context Protocol による外部サービス接続（[[tools/claude-mcp]] 参照） |
| Skill | スラッシュコマンドやエージェントスキルとして呼び出せる拡張 |
| Agent | 特定タスクに特化したカスタムエージェント定義 |
| Hook | ファイル保存・ツール実行などのイベントに自動反応するハンドラ |
| LSP | 言語サーバープロトコル（定義ジャンプ・型エラー自動検出） |

## カテゴリ別プラグイン概要

### 開発（36個）

**Anthropic 公式:**
- `agent-sdk-dev` — Claude Agent SDK アプリ開発支援（Python/TypeScript 対応ベリファイア付き）
- `feature-dev` — コード探索・設計・実装・レビューを専門エージェントで分担するワークフロー
- `frontend-design` — 高品質 UI/UX 実装支援
- `mcp-server-dev` — MCP サーバーの設計・実装・認証・配備方式選定支援
- `playground` — ライブプレビュー付き単一 HTML インタラクティブ実験場
- `plugin-dev` — プラグイン設計・実装・検証の総合キット（スキル/エージェント/フック/MCP）
- `ralph-loop` — タスク完了まで同じプロンプトを反復改善するループ開発
- `skill-creator` — スキルの作成・改善・評価・ベンチマーク支援

**代表的なサードパーティ:**
- `context7` — バージョン指定付き最新ドキュメントをコンテキストに取り込む
- `aws-serverless` — AWS SAM/CDK によるサーバーレス設計・実装・デプロイ支援
- `huggingface-skills` — OSS モデル・データセット・Spaces を使った開発・評価・学習
- `superpowers` — TDD・デバッグ・根本原因分析の強制思考フロー（[[concepts/agentic-coding]] 活用）
- `atomic-agents` — Atomic Agents フレームワークでの AI エージェント設計・実装

### 生産性（21個）

**Anthropic 公式:**
- `claude-code-setup` — 初期設定と構成提案
- `claude-md-management` — CLAUDE.md 改善・最適化支援
- `code-review` — 自動コードレビュー
- `commit-commands` — Git 操作支援
- `hookify` — フック自動生成
- `pr-review-toolkit` — PR レビュー支援（Skill + Agent）

**代表的なサードパーティ:**
- `github` / `gitlab` — GitHub/GitLab 操作
- `slack` / `notion` / `asana` / `atlassian` — Slack・Notion・Asana・Jira/Confluence 連携
- `linear` — Linear タスク管理
- `coderabbit` — AI コードレビュー支援
- `zapier` — 1,000+アプリ自動連携

### セキュリティ（5個）

- `security-guidance`（Anthropic） — 危険実装を警告する Hook
- `semgrep` — 脆弱性リアルタイム検出
- `autofix-bot` — 脆弱性・品質検出と自動修正
- `sonarqube-agent-plugins` — SonarQube コード解析連携
- `sonatype-guide` — 依存関係の脆弱性分析

### データベース（6個）

`mongodb`, `supabase`, `neon`, `pinecone`, `planetscale`, `firebase`

### デプロイ（3個）

- `vercel` — Vercel デプロイ管理（MCP + Skill + Agent + Hook）
- `railway` — Railway デプロイ
- `deploy-on-aws` — AWS デプロイ支援

### LSP（13個）

Anthropic 提供の言語サーバー（定義ジャンプ・型エラー自動検出）：

C/C++・C#・Go・Java・Kotlin・Lua・PHP・Python・Ruby・Rust・Swift・TypeScript（Anthropic公式）+ Elixir（サードパーティ）

### その他カテゴリ

| カテゴリ | 代表プラグイン |
|---------|--------------|
| テスト | `playwright`（ブラウザ E2E） |
| デザイン | `figma`（Figma 実装支援） |
| 監視 | `sentry`, `posthog`, `pagerduty` |
| 学習 | `explanatory-output-style`, `learning-output-style` |
| 自動化 | `stagehand`（自然言語ブラウザ自動化） |
| 数学 | `math-olympiad`（競技数学解法検証） |

## 関連

- [[tools/claude-code]] — Claude Code 本体
- [[tools/claude-mcp]] — MCP（Model Context Protocol）の概要
- [[concepts/agentic-coding]] — プラグインが活用されるエージェント開発スタイル
