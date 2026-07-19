# Claude Code セキュリティ設定と組織展開

メルカリ AI Security Team による [[tools/claude-code]] の組織展開戦略。エンジニアと非エンジニアの双方が安全かつ生産性高く利用できる設定を MDM で配布する方法論。

## Claude Code のリスク

- ファイルの検索・読み書き・編集
- Web 検索とページ取得
- 任意コマンドの実行（rm -rf、curl 等）

PC 上の大事なファイルや認証情報にアクセスできる → クラウドストレージ活用・認証情報の短期化でも依然リスクあり。

## セキュリティ向上設定（5点）

1. **パーミッションモードのbypass禁止** — 人間による確認を必須化
2. **危険コマンドの確認必須** — bash インライン実行・curl は許可前に確認
3. **危険な行動の禁止** — 環境変数管理ファイルの読み込み禁止、sudo 禁止
4. **Sandbox によるアクセス制限** — ディレクトリ外操作禁止、ネットワーク制限による漏洩防止
5. **セキュリティポリシーをシステムプロンプトに追加** — CLAUDE.md に記述

## 実装例：settings.json の具体的な書き方

上記5点をClaude Code公式の設定キーで実装するとこうなる（@hermes_code、2026-07-14）。キー名・書き方はAnthropic公式ドキュメントで確認したもの。

```json
{
  "permissions": {
    "disableBypassPermissionsMode": "disable",
    "deny": [
      "Bash(curl:*)",
      "Bash(wget:*)",
      "Bash(sudo:*)",
      "Read(.env)",
      "Read(.env.*)",
      "Read(**/.ssh/**)"
    ],
    "ask": [
      "Bash(git push:*)",
      "Bash(rm:*)"
    ]
  },
  "sandbox": {
    "enabled": true,
    "network": {
      "allowedDomains": ["*.github.com", "registry.npmjs.org"]
    }
  }
}
```

- `permissions.disableBypassPermissionsMode` / `disableAutoMode` を `"disable"` にすると確認スキップモード・自動モードに入れなくなる。管理者設定（後述）に置くと社員側で解除できない。
- `Bash(curl:*)` のように**丸ごと拒否**するのが公式推奨。「curlをGitHub宛てだけ許可」のように引数で細かく絞る書き方は、リダイレクト等ですり抜けられるため公式が明示的に非推奨としている。ネット取得が必要なら拒否リストで塞いだ上でWebFetch（許可ドメイン制御可）に一本化する。
- `Read(.env)` は `Read(**/.env)` と同義で、作業フォルダ配下のどの階層の`.env`も読めなくする。
- **拒否リストの限界**: Claude自身のファイル読み取りやcat等の分かりやすいコマンドには効くが、AIが書いたスクリプトが裏でこっそりファイルを開くような回り道までは止めきれない。これがサンドボックス（OSレベルで作業フォルダ外・許可外ネットワーク先への到達を物理的に遮断）が必要な理由。拒否リストとサンドボックスを重ねるのが公式の想定する最終防御。
- サンドボックスが効くのはmacOS・Linux・WSL2のみ。ネイティブWindowsは非対応のため、Windows主体の職場は権限設定（deny/ask）を厚めにする判断になる。

## MDM による全社配布

MDM（端末管理システム）を使って以下を一斉展開：
- Claude Code の設定ファイル
- システムプロンプト（CLAUDE.md）

管理者設定の置き場所はOSごとに決まっている（例: macOSは`/Library/Application Support/ClaudeCode/managed-settings.json`）。個人の`.claude/settings.json`より優先され、本人には変更できない。

**罠**: MDM 配布設定は最高優先度で扱われるため、カスタマイズしたいエンジニアと、最初から安全な状態が欲しい非エンジニアの両方を単一設定では満たせない。

## 解決策：ユーザータイプ別設定分離

| ユーザー | 設定方針 |
|---------|---------|
| エンジニア | 安全性を確保しつつカスタマイズ可能な設定 |
| 非エンジニア | 実現できうる最も安全な設定（カスタマイズなし） |

MDM に連携した情報から配布設定を分離することで、それぞれが生産性高く安全に利用できる環境を実現。

MDMを持たない中小企業でも、上記5点の最小セットを1枚の`settings.json`にまとめ「`.claude/`フォルダに置いて」と配って共有すれば同じ方向性を再現できる。強制力は落ちるが、まず`Read(.env)`の1行だけでも入れる価値が最も大きいという実務助言もある（@hermes_code）。

## エンタープライズ展開事例：NOT A HOTEL

@oikon48 による紹介（2026-05-19）。エンプラ組織での参考になる3点セット：

**① アクセス制御**
- **ドメインクレーム + Okta SSO 必須化** — テナントアクセスをIdPで一元管理
- **IdP プロビジョニング自動化** — シャドーテナント（管理外テナント）を構造的に発生させない
- 申請対応・退職時の棚卸しを自動化

**② ガードレール**（詳細は元ツイートで続報）

→ メルカリのMDM戦略と合わせて、「個人端末設定」×「テナントアクセス制御」という2層でClaude Codeのエンプラ展開をカバーできる。

## 公式security-guidanceプラグインのフック構造

`anthropics/claude-code` リポジトリに含まれる公式 `security-guidance` プラグインは、以下のライフサイクルフックで自動的にセキュリティレビューを挿入する（`hooks/hooks.json` より）：

| フックイベント | トリガー条件 | 挙動 |
|-------------|------------|------|
| `SessionStart` | セッション開始時 | `ensure_agent_sdk.py` でエージェントSDKをセットアップ（タイムアウト180秒） |
| `UserPromptSubmit` | プロンプト送信ごと | `security_reminder_hook.py` でセキュリティ注意喚起 |
| `PostToolUse` | Edit / Write / MultiEdit / NotebookEdit 後 | `security_reminder_hook.py` でパターンベース警告 |
| `PostToolUse` | Bash で `git commit:*` 実行後 | `security_reminder_hook.py` + **asyncRewake**（バックグラウンドでgit差分ベースLLMレビュー） |
| `PostToolUse` | Bash で `git push:*` 実行後 | `security_reminder_hook.py` + **asyncRewake**（プッシュ済みコミットのバックグラウンドレビュー） |
| `Stop` | セッション終了時 | `security_reminder_hook.py` + **asyncRewake**（セッション終了後のバックグラウンド総括レビュー） |

`asyncRewake: true` により、git commit/push/Stop 後もバックグラウンドでセキュリティレビューが走り、問題があればClaudeが自動的に再起動して知らせる。`rewakeMessage` でユーザーへの再起動メッセージを指定する仕組み。

## Team プラン配布時の3設定（managed settings / OTel / Hooks）

@minicoohei（2026-06-06）による **Team プラン向け**の組織展開playbook。Enterprise では監査ログ等が対応済みだが従量課金。Team プランで「配って終わり」にすると、悪意ゼロ・設定不足だけで「`.env`/鍵をうっかり読む」「`curl` で外に投げる」「`sudo` で何か無効化」が起きうる。これを最初の3設定で「安全に使える／見える／止められる」にする。

**転機は 2026/6/5**: claude.ai の権限まわりが ON/OFF トグルから **管理設定（managed settings）** に移行。トグルでは不可能だった **deny リスト**（`.env`/鍵の読取、`sudo`実行）まで Web 管理画面から全社一括強制でき、利用者は外せなくなった。

**① Team管理設定で権限を全社強制** — 組織設定 → 管理 で deny/allow を設定。評価順は `deny → ask → allow` で **deny が必ず勝つ**。`deny`＝絶対やらせない（外せない）、`allow`＝安全な定番を承認レスにして摩擦を減らす。最低ラインは `.env`・鍵・`sudo` を deny。`--dangerously-skip-permissions`（bypass）を ON のまま配ると全員が承認なしで何でも実行できる＝事故の一番太い線をここで塞ぐ。効いているかは再起動して `/status` で `Enterprise managed settings (file / remote)` が出れば設定が読まれている証拠。

**② OTel で利用状況を可視化** — Team標準UIでは「誰が・どのモデル・どのTool・何トークン」を個人粒度で追えない（監査ログ・Compliance API は Enterprise 限定）。だが Claude Code は **OpenTelemetry を標準内蔵・プラン非依存**で、管理設定の `env` に入れるだけ（①と同じ画面）で自前基盤に集約できる。Langfuse へ流すとトレースに `user.email` / 使用モデル（`claude-opus-4-8` 等）/ `input_tokens`・`output_tokens` が載り、ユーザー別に可視化できる。送り先の役割分担が定石: メトリクス（トークン量・コスト）→ Grafana/DataDog、トレース（会話・Toolの中身）→ Langfuse。可視化の本当の狙いは [[concepts/token-management]] ―― 毎回同じ手順を回す重い作業を見つけ、共通 Skill / Workflow に固める「削減の種」を探すこと。**注意**: 会話本文まで取る `OTEL_LOG_USER_PROMPTS=1` は「業務発話を全部見る」に等しいので、目的・保存期間・閲覧者を開示して同意を取ってから。まずはメタデータのみで始める。

**③ Hooks で最後の砦** — Hook も管理設定から全社強制でき、`allowManagedHooksOnly: true` で利用者の勝手な Hook を禁止できる。`PreToolUse` でコマンドの中身を検査して止める。上記「公式security-guidanceプラグインのフック構造」と同じ機構を、組織側から強制適用する形。

> 観察ログ（未検証）: 実機 v2.1.165 で bypass が弾かれ、ダメなツール呼び出しが防がれること、`/status` 表示を確認した、と著者が報告。6/5 の managed settings 移行という事実は Anthropic 当事者発信ではなく実践者の観察。

## 関連

- [[tools/claude-code]]
- [[concepts/ai-agent-governance]]
- [[concepts/workload-identity-federation]]
- [[concepts/token-management]]
- [[companies/anthropic]]
