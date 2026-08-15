# Claude Code Hooks 非同期エージェントパターン（asyncRewake）

[[tools/claude-code]] の Hooks 機能と Claude Agent SDK を組み合わせた**非同期サブエージェント起動パターン**。メインエージェントの処理をブロックせずにバックグラウンドでサブエージェントを実行し、完了後にメインエージェントへ結果を報告する。

`asyncRewake: true` を指定することで非同期起動が有効になる。

## 代表的な実装例：security-guidance プラグイン

`claude-code-plugins-official` の `security-guidance` プラグイン（[[tools/claude-code-plugins]] 参照）が実装している Hooks 設計：

```json
{
  "asyncRewake": true,
  "description": "Run security review via Claude Agent SDK"
}
```

1. Claude Code の Hook イベント（ファイル保存・ツール実行など）が発火
2. Hook が Claude Agent SDK を呼び出してセキュリティレビューエージェントをバックグラウンド起動
3. メインエージェントは処理を継続（ブロックなし）
4. セキュリティレビュー完了後、結果をメインエージェントに報告（`asyncRewake` で再起動）

## asyncRewake パターンの特性

| 項目 | 内容 |
|------|------|
| 用途 | メインフロー非遮断の非同期検査・監視 |
| 代表例 | セキュリティ脆弱性レビュー、品質チェック、ログ解析 |
| 対比 | 同期フックは応答を待つため、重い処理では UX が劣化する |

## なぜ重要か

- **コスト分離**: 重いセキュリティレビューをメインエージェントのターンコストから切り離せる
- **並列処理**: メインエージェントと検査エージェントが同時進行
- **非侵襲的監視**: 開発者のフローを妨げずに継続的な品質チェックを実現

## 関連

- [[tools/claude-code-plugins]] — security-guidance プラグインが実装例
- [[concepts/multi-agent-patterns]] — マルチエージェント設計パターン全般
- [[tools/claude-code]] — Hooks 機能の基盤
- [[concepts/agentic-coding]] — エージェント協調設計の文脈
- [[concepts/llm-japanese-style-hooks]] — 同じPostToolUseを文章品質の検査に使う運用。サブエージェント起動ではなく正規表現照合の結果を警告としてエージェント自身へ返し、処理を止めずに書き直させる（@yugen_matuni）
