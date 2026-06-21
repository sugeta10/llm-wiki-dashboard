# Workload Identity Federation

Anthropic Claude APIへの認証をOIDCトークンで行う仕組み。AWS IAM・Google Cloud・GitHub Actions等、既存のIDプロバイダーが発行する短命トークンを使い、長期有効なAPIキー（`sk-ant-...`）を排除する。

## 仕組み

1. ワークロードがIDプロバイダー（AWS STS・GCPメタデータサーバー等）から署名済みJWTを取得
2. `POST /v1/oauth/token` でAnthropicにJWTを提示（RFC 7523 jwt-bearer）
3. AnthropicがJWKS署名検証・クレームマッチング後、**短命の `sk-ant-oat01-...` トークン**を返す
4. SDKがこのトークンをAPIリクエストに使用し、有効期限前に自動更新

APIキーは「静的な資格情報」。サービスアカウントは「必要時にのみ資格情報が発行される主体」。

## 3つの設定リソース（Claude Console）

### 1. サービスアカウント（`svac_...`）
- 組織内の非人間アイデンティティ
- メール・パスワード・Consoleログインなし
- ワークスペースに追加することで、そのワークスペースのレート制限・使用帰属を持つトークンが発行される

### 2. フェデレーションイシュアー（`fdis_...`）
- 信頼するOIDCプロバイダーを登録
- `iss`クレームのURL（例: `https://token.actions.githubusercontent.com`）
- JWKSソースは `discovery`（デフォルト）、`explicit_url`、`inline`から選択

### 3. フェデレーションルール（`fdrl_...`）
- 「イシュアーXからのJWTがクレームYを持つ場合、サービスアカウントZとして動作させる」
- マッチ条件: `subject_prefix`・`audience`・exact claim値・CEL式
- トークン有効期間: 60〜86400秒（デフォルト3600秒）
- クライアントはリクエスト時にルールIDを指定（暗黙の検索なし）

## Python SDK での使用

```python
from anthropic import Anthropic, WorkloadIdentityCredentials, IdentityTokenFile

client = Anthropic(
    credentials=WorkloadIdentityCredentials(
        identity_token_provider=IdentityTokenFile("/var/run/secrets/anthropic.com/token"),
        federation_rule_id="fdrl_...",
        organization_id="00000000-0000-0000-0000-000000000000",
        service_account_id="svac_...",
        workspace_id="wrkspc_...",
    ),
)
```

環境変数 `ANTHROPIC_FEDERATION_RULE_ID`・`ANTHROPIC_ORGANIZATION_ID`・`ANTHROPIC_SERVICE_ACCOUNT_ID`・`ANTHROPIC_WORKSPACE_ID`・`ANTHROPIC_IDENTITY_TOKEN_FILE` でも設定可能（ゼロ引数形式が本番推奨）。

## クレデンシャル優先順位

1. コンストラクタ引数
2. `ANTHROPIC_API_KEY` / `ANTHROPIC_AUTH_TOKEN`
3. 明示的な `ANTHROPIC_PROFILE`
4. **フェデレーション環境変数**
5. デフォルトのアクティブプロファイル

⚠️ `ANTHROPIC_API_KEY` がフェデレーションより優先されるため、APIキーからWIFへ移行する際は環境変数を完全に削除すること。

## 対応プラットフォーム

- **AWS**: STS web identity token / EKS IRSA projected tokens
- **Google Cloud**: メタデータサーバー経由のGoogle署名済みIDトークン
- **Azure**: Managed Identity / AKS上のEntra Workload ID
- **GitHub Actions**: ActionsのOIDCトークン（キーレスCI認証）
- **Kubernetes**: Projected service-account tokens
- **SPIFFE**: SPIRE等のJWT-SVID
- **Okta**: クライアントクレデンシャルフロー

## トークンライフタイム

発行トークンの有効期間は「ルールの`token_lifetime_seconds`」と「提示したJWTの残存期間×2」の短い方（最低60秒）。SDKはキャッシュし、有効期限の5分前に更新（Kubernetes projected tokenは`ANTHROPIC_IDENTITY_TOKEN_FILE`再読み込みで自動対応）。

## 関連

- [[companies/anthropic]]
- [[tools/claude-managed-agents]]
- [[tools/ant-cli]] — 非対話ワークロードでは interactive login でなくWIFを推奨する公式CLI
- [[concepts/prompt-engineering]]
- [[concepts/claude-code-google-cloud]] — ADCによるキーレス認証でClaude on Vertex AIを使うデモ
- [[concepts/idp-shared-cli-mcp]] — IdP短命トークンを自社API宛てにCLI/MCPで共有し、人間とAIを同一身元で認証する隣接パターン
