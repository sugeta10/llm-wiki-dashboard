# ant CLI（Claude Platform CLI）

**ant CLI** は [[companies/anthropic]] が提供する Claude Platform の公式コマンドラインツール。Claude Platform の**全 API エンドポイントをターミナルから実行可能**にし、Messages API の呼び出しや [[tools/claude-managed-agents]] の起動を端末から直接行い、結果をそのままシェルにパイプできる。

API を curl で叩く代わりに専用 CLI を被せる利点は、JSON を手書きせず**型付きフラグやパイプした YAML からリクエストボディを組める**こと、`@path` 参照でファイル内容を文字列フィールドに埋め込めること、`--transform`（GJSON path）でレスポンスから必要なフィールドだけ抽出できること（jq 等の別ツール不要）、List 系エンドポイントが自動ページネーションすることにある。X Developer Platform に対する [[tools/xurl]] と同じ「公式API専用CLI」の位置づけで、コーディングエージェント（[[tools/claude-code]]）が `claude-api` スキル経由で ant CLI をネイティブに理解するため、エージェントに API 操作を任せやすい。

## 検証済み事実

- 2026-06-02: Anthropic 公式（@ClaudeDevs）が ant CLI を発表。Claude Platform の全 API エンドポイントをターミナルから実行可能、Messages API 呼び出し・Claude Managed Agents の起動・結果のシェルへのパイプに対応。Claude Code が `claude-api` スキルを使うことで ant CLI を理解する、と告知
- 2026-06-05（公式ドキュメント）: コマンド体系は `ant <resource>[:<subresource>] <action> [flags]`（例 `ant messages create`、`ant models list`）。ネストリソースはコロン区切り
- 認証は2系統。`ant auth login` がブラウザOAuthでConsoleに対し認証しトークンを workspace にスコープ（リモート/ブラウザ無し環境は `--no-browser`）。または環境変数 `ANTHROPIC_API_KEY`。複数 workspace は**named profile**（`ant profile activate <name>` / `--profile`）で切替。`ANTHROPIC_API_KEY` が設定されていると profile より優先される
- ベータ機能（agents・sessions・deployments・environments・skills）は `beta:` プレフィックス配下にあり、対応する `anthropic-beta` ヘッダを自動送出する
- 出力フォーマットは `auto`/`json`/`jsonl`/`yaml`/`pretty`/`raw`/`explore`。List・retrieve は端末接続時に**対話型エクスプローラ（TUI）**を既定で開き、パイプ時は pretty JSON に切替
- 非対話ワークロード（CI・サーバ・コンテナ）は interactive login でなく [[concepts/workload-identity-federation]] の利用が推奨されている

## 問い

- ant CLI と [[tools/xurl]]（X API用）は同じ設計思想か。Claude Code から API を叩く際、ant CLI と既存 SDK／curl のどれが最も自動化に向くか
- `claude-api` スキルが ant CLI を「よく理解している」とは具体的に何を指すか（コマンド体系がスキルに同梱されているのか）
- 自分の headless 自動化（wiki-ingest 等）で ant CLI に置き換える価値はあるか

## 関連

- [[tools/claude-managed-agents]] — ant CLI から起動できるエージェントプラットフォーム
- [[tools/claude-code]] — `claude-api` スキル経由で ant CLI を扱うコーディングエージェント
- [[tools/xurl]] — X API 用の公式CLI。同じ「公式API専用CLI」の系統
- [[concepts/workload-identity-federation]] — CI/サーバ等の非対話ワークロードで ant CLI が推奨する認証方式
- [[companies/anthropic]]
