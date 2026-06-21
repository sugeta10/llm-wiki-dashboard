# Claude Code コンテキスト階層設計

[[tools/claude-code]] の設定・コンテキスト管理は、**Memory / Slash commands / Permissions / MCP servers の4種類すべてが同一の4層階層**に従う。"Build once, use everywhere"（一度作れば、どこでも使える）が設計思想。

## 4層の定義

| 層 | スコープ | 管理者 |
|----|---------|-------|
| **Enterprise policy** | 組織全員に自動適用 | IT管理者・MDM |
| **Global (just me)** | 自分のすべてのプロジェクト | 個人 |
| **Project (shared)** | チームで共有（Gitコミット） | チーム |
| **Project (just me)** | 自分だけのプロジェクト設定 | 個人（.gitignore推奨） |

## 4種類の設定と対応パス

| 設定種別 | Enterprise policy | Global | Project (shared) | Project (just me) |
|--------|-----------------|--------|-----------------|------------------|
| **Memory** (CLAUDE.md) | `/Library/Application Support/ClaudeCode/CLAUDE.md` | `~/.claude/CLAUDE.md` | `CLAUDE.md` | `CLAUDE.local.md` |
| **Slash commands** | — | `~/.claude/commands/` | `.claude/commands/` | — |
| **Permissions** | `/Library/Application Support/ClaudeCode/policies.json` | `~/.claude/settings.json` | `.claude/settings.json` | `.claude/settings.local.json` |
| **MCP servers** | — | `claude mcp` (global) | `.mcp.json` | `claude mcp` (local) |

加えて `vendor/CLAUDE.md`（ベンダー提供設定）が存在する。`/memory` コマンドで現在読み込み中のファイルを全一覧できる。

## 各層に何を置くか

**Enterprise policy**（会社全体）
- セキュリティ制約（`.env` 読み取り禁止・`sudo` 禁止）
- 承認済みコマンドの自動許可
- 禁止URLパターン

**Global（自分の全プロジェクト共通）**
- 個人の作業スタイル・口調設定
- 個人がよく使うスラッシュコマンド
- 個人のMCPサーバー接続

**Project (shared)（チーム共有・Gitコミット）**
- プロジェクト固有のコマンド（`lint`・`test`・`build`）
- チーム共通のMCPサーバー（`.mcp.json`）
- チームのコーディング規約・アーキテクチャ文脈

**Project (just me)（個人のプロジェクト設定・.gitignore）**
- 自分だけの実験的設定
- ローカル環境固有のパス設定

## 操作ショートカット

- `/memory` — 現在読み込まれているすべての記憶ファイルを表示・選択編集
- `#` — その場で記憶を追加（どの層のファイルに保存するか選択できる）

## Enterprise policy の実用例

```
# policies.json で危険コマンドを全社員に禁止
{
  "denyTools": ["Bash(rm -rf:*)"],
  "denyUrls": ["s3.amazonaws.com"]
}
```

チーム全員が使うMCPサーバーを`.mcp.json`にコミットしておくと、誰かがそのリポジトリでClaude Codeを起動した際に自動でインストールを促す。

## 関連

- [[concepts/claude-md-rules]] — CLAUDE.md の12ルール設計・200行制限
- [[concepts/claude-code-large-codebase]] — ルートは薄く・サブディレクトリ分割の設計原則
- [[concepts/claude-code-security]] — MDMによる組織展開・ロール別設定分離
- [[tools/claude-code]] — Claude Code 全体リファレンス
- [[concepts/openai-data-agent-context-layers]] — スコープ軸でコンテキストを層分けする発想の別実装（OpenAIデータエージェントの6層設計）
