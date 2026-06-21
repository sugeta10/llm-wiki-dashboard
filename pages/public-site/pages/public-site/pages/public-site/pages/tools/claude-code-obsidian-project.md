# Claude Code × Obsidian Vault プロジェクト設計

[[tools/claude-code]] と Obsidian Vault を組み合わせた「何でも相談」プロジェクトの構成。CLAUDE.md・settings.json・MCP・スキル7本の設計思想と実践知を htani0817 が公開した Qiita 記事（2026-04-29・2026-05-18 追記あり）。

関連: [[tools/claude-code-obsidian-sync]] / [[tools/claude-mcp]] / [[concepts/claude-code-large-codebase]] / [[tools/claude-code-plugins]] / [[concepts/obsidian-personal-os]]

## 観察ログ（未検証）

- 2026-05-20: フォルダ構成の3原則 —「成果物はすべて Obsidian Vault に入れる」「置き場ルールは CLAUDE.md に書いて Claude に守らせる」「Mac/Windows 手動コピー対応ポータブル設計」
- 2026-05-20: CLAUDE.md にフォルダ配置表を1枚書くだけで Claude の出力先が安定する。フォルダルールだけでは品質が足りず、スキル利用ポリシー・文字コードルール・Obsidian 記法ルールも追記が必要（2週間運用後の知見）
- 2026-05-20: `humanizer` スキル（AI臭さ29パターン除去）と `obsidian-markdown` スキルを「常時適用」として設定すると、文章品質とフォーマットが安定する
- 2026-05-20: MCP は増やしすぎると 200k コンテキストが 70k まで縮む可能性あり（everything-claude-code README より）。プロジェクトスコープで常時有効にするのは必要最小限（著者は AWS Docs のみ）、他は必要時だけ有効化する運用が有効
- 2026-05-20: `CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING: "1"` を設定すると `effortLevel` が機能しなくなるため削除が必要。Adaptive thinking 無効化のほうが毎回安定した深さで推論させられるとのこと
- 2026-05-20: モデル変遷: Opus 4.7 → Opus 4.6（Fast mode 目的）→ Sonnet 4.6（コスト削減・日常タスクは十分）。重い推論が必要なときは `/model` でその場だけ Opus に切り替え
- 2026-05-20: Git を使わず USB/ZIP 手動コピー設計の場合、`.sh` は BOM なし UTF-8+LF、`.ps1` は BOM 付き UTF-8+CRLF、`.py` は BOM なし UTF-8+LF と明示しないと片方の OS で動かないスクリプトが量産される

## 検証済み事実

- 2026-05-20: Claude Code の `settings.json` は `~/.claude/settings.json`（グローバル）とプロジェクトルートの `.claude/settings.json`（プロジェクト）の2階層で管理できる
- 2026-05-20: `.mcp.json` は Claude Code がプロジェクトルートから読み込む仕様。Vault 内に移動すると接続が壊れる
- 2026-05-20: `settings.local.json` に個人の `permissions.allow` リストを書くと権限確認プロンプトが出なくなる（プロジェクト共有の `settings.json` と分離できる）
- 2026-05-20: カスタムスキルは `.claude/skills/<スキル名>/SKILL.md` に配置するだけで有効になる。プロジェクトスコープのスキルを自作可能
