# OpenAI Secure MCP Tunnel

> **TL;DR**: プライベートネットワーク内のMCPサーバーを、ファイアウォールのインバウンドポートを一切開けずにアウトバウンドHTTPSだけでChatGPT/Codex/Responses APIから接続させる、OpenAI公式の `tunnel-client` トンネル機能。

従来、社内MCPサーバーをChatGPT/Codexに繋ぐには何らかのインバウンド経路（パブリックリスナーやポート開放）が必要で、ここがセキュリティ部門の承認を阻む最大の障壁になっていた。本機能はこの方向を反転させ、`tunnel-client` をプライベートネットワーク内で実行してOpenAI側へアウトバウンドHTTPS接続のみで通信させる。MCPサーバーはインターネットに露出せず、インバウンドポートを一切開けないまま外部AIから到達できる。

対応製品: ChatGPT・Codex・Responses API・その他サポート済みOpenAIサービス。MCPプロトコル全般については [[tools/claude-mcp]] を参照。

## アーキテクチャ

```
OpenAI products → OpenAI-hosted tunnel endpoint
                          ↕ long-poll (outbound HTTPS only)
                    tunnel-client (inside private network)
                          ↓
                    private MCP server (no public listener required)
```

`tunnel-client` がOpenAI側のトンネルエンドポイントに対してアウトバウンドHTTPSでロングポーリングし、OpenAI製品からのリクエストを受け取ってプライベートMCPサーバーへ中継する。MCPサーバー自体はインターネットに露出しない。

接続先・パスの仕様:

- デフォルト接続先は `api.openai.com:443`。制御プレーンでmTLSを設定した場合は `mtls.api.openai.com:443`（いずれも `/v1/tunnel/*` パス）。
- ストリーミング結果（server-sent events）もトンネル経由で転送できる。
- OAuth発見（OAuth discovery）はトンネルパスを通過できる。ただし認可サーバー自体は自動的にはトンネル化されないため、認可サーバーがパブリックに到達できない構成ではOAuthフローが失敗しうる。

## tunnel-client の構成要素

- `tunnel-client` はOpenAI公式リリース（`openai/tunnel-client`）として公開されている。
- Harpoon（`tunnel-client` 組み込みのMCPサーバー）を使うと、設定済みのHTTPターゲットのみに絞った狭いスコープのHTTP呼び出しができる。汎用プロキシではなく、許可したターゲットだけに限定される点が特徴。
- 提供エンドポイント: `/healthz` / `/readyz` / `/metrics` / `/ui`（`/ui` はループバック限定のローカル管理UI）。
- 運用パターン: Kubernetesサイドカー / 専用Deployment / VMのsystemdサービス。

## セキュリティモデル

- MCPサーバーはインターネットへの公開不要（パブリックリスナー不要）。
- `tunnel-client` はアウトバウンドHTTPSのみ（インバウンドポート開放不要）。
- APIキーによる認証 + オプションで制御プレーンmTLS。
- アウトバウンドプロキシ・カスタムCAバンドル・MCP側mTLSをサポート。
- rawHTTPロギングはデフォルト無効・サポートエクスポートはリダクション済み。

このモデルにより、社内MCPサーバーを外部に露出させずにAI連携を実現できる。プライベートMCPとエージェントアクセス制御の設計観点は [[concepts/ai-agent-governance]] を参照。

## セットアップ

```bash
export CONTROL_PLANE_API_KEY="sk-..."

# stdio型MCPサーバーの場合
tunnel-client init \
  --sample sample_mcp_stdio_local \
  --profile local-stdio \
  --tunnel-id tunnel_0123456789abcdef0123456789abcdef \
  --mcp-command "python /path/to/server.py"

tunnel-client doctor --profile local-stdio --explain
tunnel-client run --profile local-stdio

# HTTP型MCPサーバーの場合
# --mcp-server-url https://mcp.internal.example.com/mcp を使用
```

## 必要な権限

| 役割 | 必要な権限 |
|------|----------|
| Runtime API Key（`tunnel-client`実行） | Tunnels Read + Use |
| Tunnel Manager（作成・編集） | Tunnels Read + Manage |

## 観察ログ（未検証）

- 2026-05-29: エンタープライズ展開における意義：「社内MCPサーバーを外に晒さずにChatGPT/Codexと繋げる」ことで、セキュリティ部門が「絶対ダメ」と言い続けてきたAI連携の障壁を解消する発表として受け止められている（@tetumemo 解説）

## 検証済み事実

- 2026-05-28: `tunnel-client` はOpenAI公式リリース（`openai/tunnel-client`）として公開（公式APIドキュメント記載）。
- 2026-05-28: デフォルト接続先 `api.openai.com:443`、制御プレーンmTLS設定時は `mtls.api.openai.com:443`（`/v1/tunnel/*` パス）。
- 2026-05-28: `/healthz` / `/readyz` / `/metrics` / `/ui`（ループバック限定のローカル管理UI）エンドポイントを提供。

## 問い

- 自分のユースケースで認可サーバーがパブリック到達不可の場合、OAuthフロー失敗をどう回避するか（認可サーバーも別途公開する/トンネル化する余地はあるか）を確認する。
- Cloudflare Tunnel や ngrok などの既存トンネルソリューションと比べ、MCP特化スコープ（Harpoon）がどこまで攻撃面を削減できるか試す。

## 関連

- [[tools/claude-mcp]] — MCPプロトコル全般・MCP仕様
- [[companies/openai]] — OpenAI製品群・ChatGPT・Codex
- [[concepts/ai-agent-governance]] — プライベートMCPとエージェントアクセス制御設計
