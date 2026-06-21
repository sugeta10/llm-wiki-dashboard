# Claude MCP（Model Context Protocol）

**MCP（Model Context Protocol）** は [[companies/anthropic]] が策定した、LLMと外部ツール・データソースを接続するためのオープンプロトコル。

## 主な用途

- Claude に外部データベース・API・ファイルシステムへのアクセスを付与
- Notion・GitHub・Slack などのサービスとの統合
- カスタムツールの呼び出し

## Notion連携ワークフロー例

1. XのブックマークをダウンロードしCSV分析してリスト化
2. Claude MCPでリスト化した情報をNotionのDBへ保存
3. 蓄積した情報を元に簡易ブログ記事を生成

→ 死蔵したブックマークを活きた情報として再活用できる

## add-mcp（一括インストールツール）

`add-mcp` は Claude Code・Codex などの AI エージェントに MCP サーバーを一括インストールするCLIツール。各エージェントの設定ファイルを自動検出・更新する。

```bash
npx add-mcp <server-name>
# 例: npx add-mcp @modelcontextprotocol/server-filesystem
```

対応エージェント: Claude Code / Codex / Cursor / Windsurf 等

## Enterprise-Managed Auth（組織管理認証）

MCPの **Enterprise-Managed Auth 拡張**に対応した（[[companies/anthropic]] 公式 @ClaudeDevs 発表、2026-06-18）。管理者が組織全体のMCPコネクタを中央で認可でき、ユーザーは初回ログイン時に必要なツールとデータがすべて接続済みの状態で使い始められる。個々のユーザーが手動でコネクタを設定する必要がなくなり、組織展開時の接続漏れや設定のばらつきを防ぐ。これは [[concepts/ai-agent-governance]] が扱う「MCP認可をどこで・誰が制御するか」という論点に対し、Anthropic公式が組織アドミン主導の中央認可レイヤーを提供する動きにあたる。

## 関連

- [[companies/anthropic]]
- [[tools/claude-code]]
- [[concepts/agentic-coding]]
- [[concepts/ai-agent-governance]]（MCP認可の限界と外部ポリシーレイヤリング）
- [[tools/openai-mcp-tunnel]] — OpenAIのSecure MCP Tunnel（プライベートMCPサーバーをトンネル経由でOpenAI製品に接続）
