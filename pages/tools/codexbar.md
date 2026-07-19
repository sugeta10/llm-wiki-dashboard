# CodexBar

**CodexBar** は、[[tools/openai-codex]]（Codex CLI）と [[tools/claude-code]] の**使用制限（レートリミット）の残量をメニューバー常駐で表示する macOS アプリ**。@steipete（Peter Steinberger、[[tools/openclaw]] の開発者）が製作し、GitHub で公開している。@taiyo_ai_gakuse が「PC 上のどこでも Codex と Claude Code の利用制限が見られる」と便利さを紹介した。

CLI で作業していると使用枠をどれだけ消費したかが見えにくく、いきなり制限に当たって作業が止まる。CodexBar はその残量を画面隅に常時可視化することで、[[concepts/claude-usage-optimization]] が説く「制限を計画的に避ける」運用を、頭の中の管理でなく UI で支える。

- リポジトリ: github.com/steipete/CodexBar
- 同じ @steipete 製のエージェント運用ツールに [[tools/skill-cleaner]]（スキル監査 CLI）、[[tools/oracle]]（Codex から上位モデル呼び出し）がある

## 対応範囲と仕組み

公式リポジトリの README（作者 steipete 本人の一次資料）によれば、CodexBar は **57プロバイダ**（Codex・OpenAI・Claude・Cursor・Gemini・Copilot・Grok・z.ai・MiniMax・AWS Bedrock・OpenRouter・LiteLLM ほか）に対応する。1プロバイダ＝1ステータス項目、または **Merge Icons モード**で1項目＋プロバイダ切替にまとめられる。各プロバイダごとに session／weekly／monthly のウィンドウと次回リセットまでのカウントダウンを出し、クレジット残高・Admin API の支出ダッシュボード・ローカルのコストスキャンを表示する。プロバイダのステータスをポーリングしてインシデントバッジをメニューとバーアイコンに重ねる。WidgetKit ウィジェットと21言語ローカライズにも対応する。macOS 14+ 専用。

## 認証とプライバシー設計

設計の核は「**既存のプロバイダセッションを再利用する**」こと。OAuth・device flow・API キー・ブラウザ cookie・ローカルファイルを流用し、パスワードは保存しない。デフォルトは on-device parsing で、ブラウザ cookie の読み取りは opt-in。作者は「ディスクを走査するわけではなく、関連機能を有効にしたときだけ既知の場所（cookie/local storage・provider config・ローカル JSONL ログ）を読む」と明言する。Full Disk Access が要るのは Safari の cookie を読むときのみで、他ブラウザ・手動 cookie・API キー・OAuth・CLI/ローカルソースで代替できる。プロバイダトークンは制限付きファイル権限の config ファイルに置かれる。

## CLI

バンドル CLI（`codexbar`）で Settings と同じプロバイダ設定をスクリプト化できる（`codexbar config providers`／`config enable --provider grok`／`config set-api-key --provider elevenlabs --stdin`）。ローカルのコスト使用量は `codexbar cost --provider codex`（`claude`／`both` も可）で取得でき、macOS・Linux 両方の CLI ビルドが提供される。コスト使用量トラッキングは [ccusage](https://github.com/ryoppippi/ccusage) にインスパイアされた、と作者はクレジットしている。

## 問い

- 使用枠の可視化は本当に「制限に当たって止まる」を減らすか、それとも枠を意識しすぎて萎縮するか。
- Claude/Codex を並行運用するとき、残量表示は [[concepts/llm-model-selection-strategy]] のような工程別モデル切り替えの判断材料になるか。

## 関連

- [[tools/openai-codex]]
- [[tools/claude-code]]
- [[concepts/claude-usage-optimization]]
- [[tools/openclaw]]
- [[tools/skill-cleaner]]
- [[tools/oracle]]
- [[tools/notch-multi-agent-monitor]] — 同じ「AIエージェントの状態を画面隅に常駐可視化する」系統だが、対象は使用制限でなくアクション要求（完了・要承認）の通知
