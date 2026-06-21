# Crabbox

> **TL;DR**: ローカルの差分をrsyncでリモートのリース済みマシンに送り、SSH経由でコマンド実行してログをストリーミングする、AIエージェント向けのリモート実行コントロールプレーン。ローカルの編集・gitワークフローを維持したまま、計算・テスト・PRエビデンス収集をクラウドへ逃がせる。

エージェントにクラウド計算を使わせる際の典型的な障害は、プロバイダーの認証情報をクライアント側に配ること（漏洩・暴走コストのリスク）と、リモート環境のためにローカルの編集・gitワークフローを捨てること。Crabboxはこの2つを、ベアラートークンだけを持つCLIと差分rsync方式で回避する。CLIはGoバイナリで、`brew install openclaw/tap/crabbox` でインストールできる。[[tools/openclaw]] のネイティブプラグインとしても機能し、`crabbox_run` / `crabbox_warmup` / `crabbox_status` 等のエージェントツールを提供する。

## 仕組みと構造

ブローカーを中心に据えた「brokered」設計を取る。各CLIはベアラートークンのみを保有し、クラウドプロバイダーの認証情報はCLIに渡らない。ブローカー（Cloudflare Worker + Durable Object）が、リースの発行、TTL境界付きマシンの管理、月次スペンドキャップ、ユーザー/組織/プロバイダーごとの使用量管理を担う。

```
your laptop          Cloudflare Worker         cloud provider
------- HTTPS -------> Fleet Durable Object --> Hetzner / AWS / Azure / GCP
   |                    lease + cost state           |
   +------- SSH + rsync to leased runner <-----------+
```

### ランの流れ（6ステップ）

1. CLIがフラグ・env・リポジトリ・ユーザー・デフォルトから設定をロード
2. CLIがリースごとのSSHキーとスラグを生成し `POST /v1/leases` をブローカーに送信
3. Workerがアクティブリースとスペンドキャップをチェック、最悪ケースTTLコストを予約し、サーバーをプロビジョニング（host/port/user/workdir/expiry/slug を返す）
4. CLIが `crabbox-ready` を待ち、可能ならリモートGitをシード、Gitファイルリストマニフェストをrsync
5. CLIがSSH経由でコマンドを実行し出力をストリーミング、実行イベントを記録・ハートビート送信
6. `--keep` がない限りリースをリリース（保持リースもアイドルタイムアウト後にブローカーが解放）

## 主な機能

- **ウォームボックス再利用**: `crabbox warmup` でボックスをウォーム状態に保ち、`--id` を付ければ複数ラン・SSH・CI間で同じボックスを再利用できる。
- **GitHub Actionsのローカル再現**: `actions hydrate` でリポジトリのGitHub Actionsセットアップ手順を再利用し、同一のハイドレート済みワークスペースでローカル実行できる。
- **リモートデスクトップのブラウザ配信**: `crabbox webvnc` でLinux/macOS/Windowsデスクトップをブラウザにストリーミングできる。VPN不要で、UIテスト・スクリーンショット取得・ライブセッション共有に使える。
- **PRエビデンスのバンドル**: `crabbox artifacts collect` でスクリーンショット・動画・JUnitサマリー・ログ・リースメタデータをバンドルし、PRエビデンスとして添付できる。
- **マルチプロバイダー対応**: Hetzner / AWS / Azure / E2B / Daytona / Blacksmith / Semaphore / GCP / Proxmox / Parallels / Modal / Cloudflare 等に対応。

## クイックスタート

```sh
crabbox login --url https://broker.example.com  # 1回のみ
crabbox run -- pnpm test                        # 新規リースで1回実行
crabbox warmup                                  # ウォームボックスを起動
crabbox run --id blue-lobster -- pnpm test:changed
crabbox ssh --id blue-lobster
crabbox stop blue-lobster
```

## 検証済み事実

- 2026-05-28: CLIはGoバイナリ。ブローカーはCloudflare Worker + 単一Durable Object（公式サイト記載）
- 2026-05-28: `brew install openclaw/tap/crabbox` でインストール可能（公式）
- 2026-05-28: [[tools/openclaw]] のネイティブプラグインパッケージとしてリポジトリルートに含まれる（Linuxランナーは vanilla Ubuntu / cloud-init 準備済み）

## 観察ログ（未検証）

- 2026-05-28: 対応プロバイダーとして Hetzner / AWS / Azure / E2B / Daytona / Blacksmith / Semaphore / GCP / Proxmox / Parallels / Modal / Cloudflare 等が挙げられているが、各プロバイダーの実際の稼働状況・完成度は単一ソース記載のみで未確認

## 問い

- 既存のCIランナー（GitHub Actions等）と比べて、ローカル差分rsync方式はどの程度フィードバックが速いか実測してみる
- `webvnc` でのUIテストやスクリーンショット取得を、自分のエージェントワークフロー（[[tools/claude-code]] 連携）に組み込めるか試す

## 関連

- [[tools/openclaw]] — CrabboxはOpenClawのネイティブプラグインとして動作（`crabbox_run` / `crabbox_warmup` / `crabbox_status` 等）
- [[concepts/agentic-coding]] — AIエージェントがリモートコンピューティングリソースを使う文脈
- [[tools/claude-code]] — Crabboxをオーケストレーター経由でClaude Codeと組み合わせ可能
- [[tools/claude-code-remote-control]] — 計算をクラウドへ逃がすCrabboxに対し、自分のPCを出先から遠隔操作する逆方向のリモート運用
