# Claude Code × Obsidian 自動同期

[[tools/claude-code]] の会話履歴を自動的に Obsidian の Markdown ファイルとして保存する仕組み。Zenn（pepabo/kentaro）による実装例。[[concepts/llm-wiki]] とも親和性が高い。プロジェクト全体設計については [[tools/claude-code-obsidian-project]] も参照。

## 仕組み

```
Claude Code セッション
    ↓ (jsonlに記録)
~/.claude/projects/*/session.jsonl
    ↓ (5秒ごと監視)
watch-and-save.sh (LaunchAgent常駐)
    ↓ (変更検知 → 抽出・整形)
~/obsidian/claude/YYYY年M月D日.md
    ↓ (自動 git commit)
GitHub同期
```

## 実装のポイント

### セッションファイルの場所

Claude Code は会話履歴を `~/.claude/projects/{project-hash}/{session-id}.jsonl` に JSONL 形式で保存する。このファイルをパースして会話を抽出する。

### 追記モード

上書きではなく追記モードを採用。`LAST_LINE_FILE` に最後に同期した行番号を保存し、新しい行だけを処理して既存の Obsidian ファイルに追記する。

- 他プロセスとの競合を防止
- クラッシュしても記録が失われない
- 5秒ごとに新しい会話だけを追記

### ノイズ除去

`jq` を使って以下を除外：
- `<system-reminder>` 等のシステムメッセージ
- `<local-command-...>` のローカルコマンド出力
- `subagents/` 配下のサブエージェントセッション

### 日付ベースのフィルタリング

各エントリのタイムスタンプを確認して当日の会話だけを抽出。日をまたいだセッションにも対応。

## LaunchAgent 常駐化

```xml
<!-- ~/Library/LaunchAgents/com.claude.obsidian-sync.plist -->
<key>KeepAlive</key><true/>
<key>RunAtLoad</key><true/>
```

`launchctl load` で登録することで macOS 再起動後も自動起動。

## 出力例

```markdown
# 2026年1月9日 Claudeとの会話

**ユーザー**: uvを入れて
**Claude**: uvをインストールします。
**Claude**: uv 0.9.22 がインストールされました。
```

## 今後の展望

- トピックごとの自動分類
- 要約の自動生成
- スマホの Claude アプリからの会話も GitHub API 経由で統合

## 関連

- [[tools/claude-code]]（会話履歴の保存元）
- [[concepts/llm-wiki]]（Obsidian ナレッジベースとの統合）
