# Private MCP Tunnels（OpenAI）

**Private MCP Tunnels** は、企業のネットワーク内に置いたプライベートMCPサーバーを、ChatGPT・Codex・Responses APIからアウトバウンドのみのHTTPS接続で利用できるようにするOpenAIのセキュアトンネル機能。

公式ドキュメント: https://developers.openai.com/api/docs/guides/secure-mcp-tunnels

## 仕組み

```
[社内ネットワーク内のMCPサーバー]
         ↓ アウトバウンドHTTPS（外向きのみ）
[OpenAIトンネルエンドポイント]
         ↓
[ChatGPT / Codex / Responses API]
```

- MCPサーバーはインターネットに直接公開しなくていい
- アウトバウンドのみのHTTPS接続 → ファイアウォールルール変更が最小限
- ChatGPT・Codex・Responses APIすべてから接続可能

## 意義

既存のMCP設計では、MCPサーバーがパブリックに到達可能である必要があった（または社内クライアントのみに限定する必要があった）。Private MCPトンネルにより、機密性の高い社内データ（顧客DB・社内API・独自知識ベース等）を持つMCPサーバーを、セキュリティを保ちながらOpenAI製品に接続できるようになる。

## 関連

- [[tools/claude-mcp]] — Anthropic側のMCPプロトコル実装・比較対象
- [[companies/openai]] — この機能の提供元
- [[concepts/ai-agent-governance]] — MCPの認可・セキュリティ設計の概念
