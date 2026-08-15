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

## MCP 2026-07-28スペックとClaudeでの展開

[[companies/anthropic]] は2026-07-28、MCPの5回目のスペックリリース「MCP 2026-07-28」を発表した。MCPは今年だけで月間SDKダウンロード数が4倍の400M超に伸び、AIエージェントとアプリケーションを繋ぐ業界標準になったとAnthropicは位置づける。今回のスペックは3点を変更する。

- **ステートレス化**: MCPを双方向ステートフルなプロトコルからリクエスト/レスポンス型のステートレスコアへ移行。サーバーをサーバーレス・エッジ基盤にデプロイしやすくなる
- **拡張機能の標準化**: MCP AppsとTasksがバージョン管理された拡張フレームワークの下で正式仕様になり、コア仕様を変えずにインタラクティブUIや長時間実行処理を追加できるようになった
- **認可の強化**: OAuth 2.0・OIDCの本番運用に合わせ、Entra・OktaなどエンタープライズIDシステムへ回避策なしで接続できるようにした

Claude製品側でも対応する機能が揃った。Anthropicは、コネクタディレクトリに950以上のMCPサーバーが並び日々数百万人に使われている状況を踏まえ、以下を挙げる。

- **Observability**: コネクタディレクトリに公開したMCPサーバーの採用状況・エラー・レイテンシを追跡できる、開発者向けダッシュボード機能
- **MCP tunnels（research preview）**: プライベートネットワーク内のMCPサーバーを、インバウンドのファイアウォール開放や公開エンドポイントなしにClaudeへ接続する機能。[[tools/openai-mcp-tunnel]]（OpenAI Secure MCP Tunnel）と同種のアウトバウンドトンネル型アプローチ

MCP Appsは[[concepts/generative-ui]]が扱うOpen-endedパターンの実装にあたり、Enterprise-managed authは本ページ上記の節を参照。

## 問い

- ステートレスコア化は既存のMCPサーバー実装にどの程度の移行コストを強いるか？
- MCP tunnelsと[[tools/openai-mcp-tunnel]]は今後どちらが標準になるか、それとも共存するか？

## 関連

- [[companies/anthropic]]
- [[tools/claude-code]]
- [[concepts/agentic-coding]]
- [[concepts/ai-agent-governance]]（MCP認可の限界と外部ポリシーレイヤリング）
- [[concepts/generative-ui]]（MCP AppsのOpen-endedパターン）
- [[tools/openai-mcp-tunnel]] — OpenAIのSecure MCP Tunnel（プライベートMCPサーバーをトンネル経由でOpenAI製品に接続）
- [[tools/claude-tag]] — SlackにClaudeをチームメイトとして常駐させる製品（ツール/チャンネルアクセスの付与を伴う）
- [[concepts/agentic-data-analytics]] — MCPを業務データ分析の接続層として使う設計論。デジタル庁が行政手続等調査データ約75,000件をMCP経由で自然言語分析する実装をGitHub公開した事例を含む
