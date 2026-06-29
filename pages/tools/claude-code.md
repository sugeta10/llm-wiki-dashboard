# Claude Code

**Claude Code** は [[companies/anthropic]] が開発したAIコーディング CLI ツール。ターミナルから直接コードの生成・編集・実行を行える。

Obsidian Vault との組み合わせ設計については [[tools/claude-code-obsidian-project]] を参照。言語別コスト・速度ベンチマークは [[concepts/ai-coding-lang-benchmark]] を参照。技術書をスキルに自動変換するには [[tools/book-to-skill]] を参照。

## インストール方法

公式推奨インストール手順（npmは**非推奨**）：

| プラットフォーム | コマンド |
|--------------|--------|
| macOS / Linux（推奨） | `curl -fsSL https://claude.ai/install.sh \| bash` |
| macOS / Linux（Homebrew） | `brew install --cask claude-code` |
| Windows（推奨） | `irm https://claude.ai/install.ps1 \| iex` |
| Windows（WinGet） | `winget install Anthropic.ClaudeCode` |
| npm（非推奨） | `npm install -g @anthropic-ai/claude-code` |

## プラグインシステム

Claude Code はプラグインによる機能拡張をサポートしている。公式マーケットプレイス `claude-plugins-official` に **123個のプラグイン**（MCP・Skill・Agent・Hook・LSP）が掲載されている。詳細は [[tools/claude-code-plugins]] を参照。

## 主な特徴

- **Skills / MCP / Agent** などの核心概念を持つ拡張可能な設計
- **hidden / under-utilized features** が多く、上級者向けのワークフロー最適化が可能
- スキル管理に `gh skill`（GitHub公式スキル管理ツール）を使用可能
- [[concepts/agentic-coding]] の実装プラットフォームとして活用される

## ベストプラクティス

GitHubで急拡散した「Claude Code Best Practice」リポジトリには以下がまとまっている：
- 検証済みの高効率ワークフロー
- ハマりやすいポイントと回避策
- Skills・Agent・MCPなど核心概念の体系的解説

## Anthropic公式のマルチエージェント構成

[[companies/anthropic]] が公開したマルチエージェントフレームワークでは：
- 人間1人が指示役、AI複数体が業務分担
- タスクをAIが自律的に進める完全自動化ワークフロー

## Routines（定時自動実行）

Claude Code デスクトップアプリの **Routines** 機能で定時プロンプトを自動実行できる。[[concepts/llm-wiki]] との組み合わせが特に有効：

- **設定項目**: Name・Prompt・Frequency（曜日・時刻）・Working folder・Permission mode
- **用途**: 日報自動生成 → LLM Wiki への ingest を1プロンプトで完結
- **MCP コネクタ**: Slack / GitHub / Google Calendar / Gmail / Jira / Confluence 等と連携
- **権限管理**: 読み取り系のみ「常に許可」、書き込み系は「承認が必要」に設定するのが推奨

詳細: [ServerWorks ブログ](https://blog.serverworks.co.jp/claude-code-llm-wiki)

## コンテキスト管理とセッション引き継ぎ

Claude Codeの最大制約はコンテキストウィンドウの速い飽和。公式も認めるアンチパターン：

> **The kitchen sink session** — 単一セッションで複数タスクを往復すること

推奨運用：タスク区切りで `/clear` によるコンテキストリセット。

### /handover カスタムコマンド

`~/.claude/commands/handover.md` に定義するスラッシュコマンドで、セッション間の引き継ぎノートを自動生成する（[classmethod 解説記事](https://dev.classmethod.jp/articles/claude-code-session-handover/)）。

引き継ぎノートの核心は **「捨てた選択肢と理由」** セクション。次セッションでの無駄な再議論を防ぐ。

出力先：`.claude/handovers/YYYY-MM-DD_HHmm.md`（タイムスタンプ付きで蓄積）

CLAUDE.md に以下を記載することでセッション開始時に自動読み込み：
```
セッション開始時に `.claude/handovers/` の最新ファイルを読み込む
```

## パーソナライズ・育て方

note 記事「[大量に流れてくるテクニック情報は追わなくていい](https://note.com/kajiken0630/n/n81cddc4d5e4e)」（有料・kajiken0630）によると：
- 毎日流れるテクニック情報を全部追う必要はない
- 特定のアプローチで「自分にとって最強のバディ」に育てられる
- そのまま使えるプロンプトを提供（本文有料 ¥2,980）

[[concepts/prompt-engineering]] も参照。

## HTML出力の活用

「**The Unreasonable Effectiveness of HTML**」— Claude Code を使った成果物としてHTMLが特に有効という知見が広まっている。

- HTMLはブラウザで即プレビューでき、対話的なダッシュボード・レポート・可視化ツールを素早く作成できる
- Markdown や JSON より表現力が高く、Claude Codeのコーディング能力を最大限に活用できる形式
- HTMLはファイルシステム・MCP・git履歴など広いコンテキストを取り込める Claude Code との相性が特に良い
- バージョン管理（diffがノイジー）以外のデメリットはほぼない
- Opus 4.7 の 100万トークンコンテキストにより、HTMLの追加トークン消費はほぼ問題にならない

### ユースケース別ギャラリー（[html-effectiveness](https://thariqs.github.io/html-effectiveness)）

| カテゴリ | 説明 |
|---------|------|
| Exploration & Planning | 複数の方向性を横並び比較、実装計画をタイムライン+図で表現 |
| Code Review & Understanding | アノテーション付きdiff、PR説明書、モジュールマップ |
| Design | デザインシステムトークンのスウォッチ表示、コンポーネントバリアント一覧 |
| Prototyping | アニメーションサンドボックス（スライダー付き）、クリックスルーフロー |
| Illustrations & Diagrams | インラインSVGで図表・フローチャート・デプロイパイプライン |
| Decks | キーボードナビ付きスライドデッキ（ビルドステップ不要） |
| Research & Learning | 折りたたみセクション・タブ・用語集付きインタラクティブ解説 |
| Reports | ウィークリーステータス、ポストモーテムタイムライン |

### Playgrounds

「**Making Playgrounds using Claude Code**」— @trq212 による応用パターン。

- スライダー・ノブ等のUIコントロールを埋め込んだ**使い捨てエディタ**としてHTMLを活用
- パラメータをGUI上でチューニングし、「コピーとしてプロンプト」ボタンでClaude Codeに戻す双方向ワークフロー
- 用途例: アニメーションのイージング調整、フィーチャーフラグ設定エディタ、プロンプトテンプレートライブプレビュー

詳細: [Using Claude Code: The Unreasonable Effectiveness of HTML](https://x.com/i/article/2052796100608974848)

## Artifacts（セッションから生成する共有ページ）

[[companies/anthropic]] 公式（@claudeai）が2026-06-18に発表したベータ機能。セッションの内容からインタラクティブなページを生成し、プライベートリンクでチームに共有できる。これまで手作りしていた上記「HTML出力の活用」の成果物共有を、公式機能として取り込んだ位置づけ。

- **用途例**: PR ウォークスルー、刻々と更新される「リビング」プロジェクトダッシュボード
- **共有方法**: プライベートリンクでチームに公開
- **提供範囲**: Team / Enterprise プランでベータ提供

セッションのフロー構造を文書化して受け渡す手法（[[concepts/llm-doc-management]]）と問題意識が重なるが、Artifacts はその出力を共有可能なページとして公式に提供する点が異なる。

## 5つのアーキテクチャ層

「Claude Code ships with 5 architectural layers most engineers never open」— LLMだけでは解けない問題を各層が担う設計：

1. **Model layer** — LLM推論
2. **Tool layer** — ファイル・シェル・ブラウザ操作
3. **Agent layer** — マルチステップ自律実行
4. **Harness layer** — 品質保証ループ（Default-FAIL contract等）
5. **Orchestration layer** — 複数エージェントの協調

この層構造が [[tools/claude-harness]] の設計基盤になっている。

## 非コーディング用途の5パイプライン

「5 pipelines I'd sell today using Claude Code (none of them are coding)」：
- リサーチ・情報収集の自動化
- レポート・要約の生成
- データ変換・クレンジング
- ドキュメント作成・更新
- メール・コミュニケーション下書き

## 情報収集での活用（Claude Codeで情報収集する4つのこと）

1. URLやPDFをClaude Codeに直接渡す
2. WebFetch でページ取得 → 要約（内部実装詳細は [[concepts/claude-code-webfetch]] 参照）
3. 取得内容を構造化してMarkdownに保存
4. wiki/ナレッジベースへ自動 ingest（[[concepts/llm-wiki]] 参照）

## セキュリティ設定と組織配布（メルカリ事例）

メルカリの AI Security Team（@hi120ki）による全社展開戦略（Claude Code Meetup Japan #4, 2026-04-10）。

### リスク認識

Claude Code が持つ能力（ファイル読み書き・Web取得・コマンド実行）は、悪用されると：
- PC上の重要ファイル・認証情報の漏洩
- `rm -rf` 等の破壊的コマンド実行
- `curl` 経由でのデータ外部送信

### セキュリティ向上設定5原則

1. **人間による確認を必須化** — パーミッションモードの `bypass` を禁止
2. **危険コマンドの確認強制** — bash インライン実行・curl は必ず確認
3. **禁止アクション定義** — 環境変数管理ファイルの読み込み禁止、`sudo` によるシステム変更禁止
4. **Sandbox 制限** — ディレクトリ外操作禁止・ネットワーク制限で漏洩防止
5. **セキュリティポリシーの注入** — 会社ポリシーをシステムプロンプトに追加

### MDMによる全社展開

MDM（端末管理）を使って Claude Code 設定・CLAUDE.md（システムプロンプト）を一斉展開。

**課題**: エンジニア（カスタマイズしたい）と非エンジニア（最初から安全な状態が欲しい）で最適設定が異なる。

**解決策**: MDM から配布設定を分離し、ロール別に設定を配布：
- **エンジニア向け**: セキュリティを確保しながらカスタマイズ可能な設定
- **非エンジニア向け**: 最大限に安全な設定をカスタマイズなしで提供

## Boris Cherny 公式ワークショップ（2026年5月）

Claude Code 開発者 Boris Cherny による Anthropic 公式ワークショップの Tips 全7点:

### Tips 1-2: コードベースQ&Aからはじめる

- **新メンバーオンボーディング**: 最初の課題はコードについて質問させること。いきなりコード編集させない
- Anthropicの技術オンボーディングが「2〜3週間 → 2〜3日」に短縮
- Q&A練習で「どこまで一人でやれるか・どこで助けが必要か」の境界線を把握する

### Tips 3-4: ワークフローを自分に合わせる

推奨ワークフローパターン：

1. **Explore → Plan → Confirm → Code → Commit** — 大きな機能実装の基本
2. **Write tests → Commit → Code → Iterate → Commit** — TDD流
3. **Write code → Screenshot → Iterate** — UIをPuppeteerで撮影して反復改善

Claudeに「自分の結果を確認できるツール」（テスト・スクリーンショット等）を与えると自律的に反復改善できる。

### Tips 5-6: コンテキスト階層の設計

Memory / Slash commands / Permissions / MCP servers の4種類すべてが同一の4層（Enterprise → Global → Project shared → Project local）に従う。詳細は [[concepts/claude-code-context-hierarchy]] を参照。

`/memory` コマンドで現在読み込まれているすべての記憶ファイルを確認できる。`#` でその場で記憶を追加するとどのファイルに保存するか選択できる。

### Tip 7: チーム設定をGitにコミット

MCP サーバー・スラッシュコマンド・権限設定をプロジェクトの `.claude/` 以下に置いてGitコミットすることで、チーム全員が自動的に同じ設定で使える。

### キーバインディング

| キー | 動作 |
|-----|------|
| `Shift + Tab` | ファイル編集を自動承認モード（bash は要確認のまま） |
| `#` | その場でCLAUDE.mdに記憶を追加 |
| `!` | bash モード（実行結果がClaudeのコンテキストに入る） |
| `Esc` | 安全に作業を停止 |
| `Esc × 2` | 履歴を1つ前に戻る |
| `Ctrl + R` | 完全な出力を表示 |

### Claude Code SDK

`claude -p "プロンプト" --allowedTools Bash(git log:*) --output-format json` で他コマンドとパイプ接続可能。CIや自動化パイプラインに組み込める。

### 音声入力

macOSのDictation（アクセシビリティ > 音声入力）をONにし、dictationキーを2回押すことで声でプロンプトを入力できる。他のエンジニアに話しかけるように使える。

## Boris（生みの親）の30 Tips

Claude Code開発者 Boris Chen の実践Tips（2026年4月時点）より主要点：
- **CLAUDE.md 最重要**: プロジェクトのコンテキストを詳細に記述するほど精度向上
- **小さくコミット**: 大きなタスクは必ず分割。途中でコンテキストが詰まる
- **Tools の制限**: 不要なツール権限を与えない（安全性・精度向上）
- **ループ検知**: 同じミスを3回繰り返したら `/clear` で仕切り直す

## Claude Code集中学習ガイド（kajiken0630）

梶谷健人による14ステップ学習ロードマップ（2026年5月）。「全体像をつかみ、土台を整え、育て方を学び、応用とチーム展開まで広げる」流れ。

| ステップ | テーマ |
|---------|------|
| 1–2 | 全体像の把握（非エンジニアにこそ必要な理由）・経営層向け動画 |
| 3–4 | カスタマイズ・ショートカット・実演動画 |
| 5–7 | 育て方・コンテキスト飽和対策（Effort Levelのデフォルト変更等） |
| 8 | Skills × MCP × トリガー（Cron/GitHub Actions）で自動化ワークフロー構築 |
| 9–10 | スライド作成（Webサイトとして作る発想 / Claude Designとの組み合わせ） |
| 11–12 | Claude CodeをWebサービスのバックエンドとして活用 |
| 13 | AIで高デザイン性サイトを作る8ポイント |
| 14 | チーム共有・同期の仕組み設計（GitHubの作法を学習させずに解決） |

連休明けには「Claude Codeが業務環境の真ん中に座っている状態」を目標とした実践構成。

## Enhanced Claude Code (ECC) エコシステム

Anthropic ハッカソン優勝者が構築・公開した Claude Code 拡張キット（GitHub 153,000+ stars）。

### 主要コンポーネント

- **38エージェント**: 各エージェントは単一役割を担う専門家。TypeScript, Python, Go, Java 等12言語対応。Planner エージェントがタスクを分解・各エージェントに割り振る
- **156スキル**: コンテキストウィンドウを常時消費せず、必要時のみロード
- **72コマンド**: `/review`・`/scan` 等のスラッシュコマンドで長いプロンプト入力を1スラッシュに圧縮
- **AgentShield**: セキュリティ監査ツール。1,282テスト・98%カバレッジ・102セキュリティルール。赤チーム/青チーム/監査員の3エージェントパイプライン。CI組み込み可能

### 継続学習システム

通常 Claude Code はセッションをまたいで学習しないが、ECC はセッション観察から「instinct」と呼ぶ行動パターンを知識層として蓄積する。2〜3週間の日常使用で自分のコーディングスタイルを習得するとされる。

### 補完リポジトリ

| リポジトリ | Stars | 役割 |
|-----------|------|-----|
| claude-mem | 38,000+ | セッション間メモリ永続化（SQLite + Webビューア） |
| Superpowers | — | TDD・ブレインストーミング・根本原因デバッグの強制思考フロー |

開発者報告: 60%コスト削減、3〜5倍の出荷速度向上。

## エージェント化事業例: LP制作

Claude Code エージェントを使ったランディングページ制作の自動化ビジネス事例（2026年5月）：

1. Google Maps で見込み客（サイトなし・古いサイト）を特定
2. Lovable 上で各社向け LP モックアップを自動生成（短い動画も自動生成）
3. 業種別にカスタマイズした営業文を自動作成

**実績**: 1人で月47社を受注、1社あたり $400 請求。[[concepts/agentic-coding]] の実ビジネス活用事例。

## 知られていない上級設定15項目

（2026年5月時点 / zodchiii, Boris Cherny 確認）

| # | 設定 | 問題 | 対応 |
|---|-----|-----|-----|
| 1 | **Effort Level** | 2026年3月にデフォルトが high → medium に無告知変更 | `ANTHROPIC_EFFORT=high` をシェル設定に追記 |
| 2 | **Adaptive Thinking** | easyタスクで思考をスキップ、バグ見落としが増える | 固定推論予算を設定して毎ターン思考を強制する |
| 3 | **Permission Mode** | デフォルトは毎ツール呼出で確認（1日47回確認の報告も） | `settings.json` で `acceptEdits` / `plan` / `auto` を設定 |
| 4 | **Allow/Deny Rules** | 無設定だと `.env`・`.ssh` も読み取り可能 | `allowedTools`・`deniedTools` で明示制御 |
| 5 | **Model Switching** | 全タスクを Opus で実行すると Sonnet 比5倍のコスト | セッション途中でモデル切替可。80%のタスクは Sonnet で十分 |
| 6 | **Compact 指示** | デフォルトの `/compact` はプロジェクト固有コンテキストを捨てる | カスタム指示で保持すべき情報を明示 |
| 7 | **Memory** | セッション間でパターンを忘れる | `~/.claude/projects/<project>/memory/` に手動追加可 |
| 8 | **Auto-format フック** | 毎編集後の手動フォーマットが煩わしい | `settings.json` の `postToolUse` フックで自動実行 |
| 9 | **前処理フック** | 10,000行ログを全部渡すと大量トークン消費 | ログ前処理フックで50行に絞り込んで渡す |
| 10 | **Git Worktree 分離** | 編集が作業ブランチに混入する | Worktree モードで Claude が別ブランチに隔離作業する |
| 11 | **Bare Mode** | 起動ごとに CLAUDE.md 等を全スキャンするのが低速 | `--bare` で最小ロード（素早い単発質問向け） |
| 12 | **Budget Cap** | CI/CD での無限ループでトークンが消え続ける | `--max-budget-usd 5` でタスク単位の上限設定 |
| 13 | **Thinking Summaries** | UI がデフォルトで思考を要約表示するため推論量が少なく見える | `--thinking-summaries off` で全思考表示 |
| 14 | **Parallel Subagents** | タスクに対して20+エージェントが並行起動することがある | プロンプトでエージェント数を明示指定 |
| 15 | **MCP Server トークン** | 5サーバー接続で90,000トークン/ターンのオーバーヘッド | 未使用 MCP サーバーを削除し1ターンあたりのコストを削減 |

## CLAUDE.md 行動ルール設計

[[concepts/claude-md-rules]] を参照。Karpathy の4ルール + 8ルール追加で、Claude のミス率を 41% → 3% に削減する手法が体系化されている。

## 自律ワークフロー：/goal コマンド

[[tools/claude-code-goal]] を参照。完了条件を設定して Claude が自律的に条件を満たすまで繰り返すセッションスコープ機能。

## フォルダ構成

ホームディレクトリ（`~/.claude/`）に作成される主要な構造：

```
~/.claude/
├── CLAUDE.md              — 個人ルール
├── agent-memory/          — サブエージェント用記憶領域
├── agents/                — サブエージェント定義
├── commands/              — 個人スラッシュコマンド (command-name.md)
├── hooks/                 — イベントフック
├── plugins/               — プラグイン（マーケットプレイス含む）
├── projects/              — プロジェクト別会話履歴・メモリ
├── settings.json          — 個人設定
└── skills/                — 個人スキル (skill-name/SKILL.md)
```

プロジェクトフォルダ（`myapp/.claude/`）：

```
.claude/
├── agents/                — プロジェクト固有サブエージェント
├── commands/              — プロジェクト固有コマンド
├── settings.json          — プロジェクト共通設定
├── settings.local.json    — 個人のプロジェクト設定（.gitignore 推奨）
└── skills/                — プロジェクト固有スキル
```

## スラッシュコマンド一覧（補足）

Boris Cherny ワークショップで言及済みの主要コマンドに加え、知っておくと便利なもの：

| コマンド | 動作 |
|---------|-----|
| `/context` | 現在のコンテキスト使用量を確認 |
| `/compact [prompt]` | 会話履歴を要約して圧縮。`prompt` で「未対応TODOは保持して」等を指定可 |
| `/export [file]` | 会話履歴をクリップボードまたはファイルにエクスポート |
| `/rewind` | チェックポイント一覧を表示し、選択した時点に巻き戻す（1指示＝1チェックポイント自動生成） |
| `/branch [name]` | セッションを分岐作成 |
| `/rename [name]` | セッション名を変更 |
| `/resume [name]` | 過去のセッションを再開（`--resume UUID` オプションでも可） |
| `/recap` | セッションの1行要約を生成 |
| `/add-dir path` | 現在のセッションに作業ディレクトリを追加 |
| `/extra-usage` | 固定料金プラン上限超過時にAPI従量課金で継続するモードのON/OFF |
| `/privacy-settings` | セッションデータをAnthropicの訓練に使用するかの許可切り替え |
| `/tui [mode]` | UIモード切替（`default` / `fullscreen`） |
| `/focus` | フォーカスモード：最終プロンプトと最終応答のみ表示（フルスクリーン専用） |
| `/statusline` | プロンプト入力欄下のステータスラインカスタマイズ |
| `/voice [mode]` | 音声入力のOn/Off切替。`hold`（長押し）/ `tap`（タップ）/ `off`。スペースキー長押しで音声入力 |
| `/loop [interval] [prompt]` | プロンプトを繰り返し実行。interval: `5m`/`2h`/`1d`等。省略時はClaude自動調整。7日で自動停止 |
| `/batch instruction` | 指定した大規模変更を複数サブエージェントで**並行実行** |
| `/remote-control` | このセッションをリモートから操作可能にする。URL発行→スマホ等からセッション操作・進捗確認。4レベルの運用法は [[tools/claude-code-remote-control]] を参照 |
| `/ultraplan [prompt]` | Claude Code on the Web上でOpusによる大規模改造計画を立案。その間ターミナルで別作業が可能 |
| `/insights` | 過去のセッションを分析し、利用方法の分析・改善提案を含む**HTML形式レポート**を生成 |
| `/team-onboarding` | 過去30日の使用履歴からチーム新メンバ向けオンボーディングガイド（Markdown）を自動生成 |
| `/debug [description]` | デバッグログを有効化。スキル/コネクタ呼出失敗やHook未起動の原因調査に使用 |
| `/btw question` | 処理実行中に処理に影響しない質問を行う（by the way） |
| `/doctor` | インストールや設定状況を診断 |

> **使い分け**: セッションが長くなったら `/compact`（文脈を保ちたい場合）または `/clear`（完全リセットしたい場合）。新タスク開始時は必ずどちらかを使う。

## GitHub連携ツール5選（無料）

`@kirillk_web3`（2026-05-14）の記事「5 GitHub Tools That Will Change How You Use Claude Code — All Free.」で紹介されたGitHub上の無料ツール群。Claude Codeの使い方を変える実用ツールとして注目を集めている。

詳細はソース記事（X記事形式）を参照。

## コードベース文書化パターン

Claude Codeにアプリのフロー構造をHTML + JSONで文書化させ、そのJSONを次のタスクのコンテキストとして渡す実践パターン。詳細は [[concepts/llm-doc-management]] を参照。

## 関連

- [[companies/anthropic]]
- [[concepts/agentic-coding]]
- [[concepts/vibe-coding]]
- [[tools/cursor]]
- [[tools/claude-harness]]
- [[tools/xurl]]（X API連携）
- [[tools/claude-managed-agents]]（Anthropicのエージェント管理プラットフォーム）
- [[concepts/claude-code-security]]（メルカリの組織展開戦略）
- [[concepts/developer-ai-collaboration]]（韓非子×Claude Code活用哲学）
- [[concepts/claude-md-rules]]（CLAUDE.md 12ルール設計）
- [[tools/obsidian-skills]]（kepano製 Obsidian向けAgent Skills集）
- [[tools/claude-code-obsidian-sync]]（会話履歴をObsidianに自動記録する仕組み）
- [[tools/gstack]]（Garry Tan によるClaude Code拡張スキル集・バーチャルエンジニアリングチーム化）
- [[tools/claude-code-goal]]（/goal コマンド：自律ワークフロー）
- [[tools/claude-code-plugins]]（公式プラグイン123個の解説）
- [[concepts/claude-code-webfetch]]（WebFetch の内部実装・Haiku 要約の仕組み）
- [[concepts/claude-code-large-codebase]]（大規模コードベースでの活用ベストプラクティス・ハーネス5層設計）
- [[concepts/claude-usage-optimization]]（トークン使用制限回避・モデルスタッキング・チャット長管理の5ステップ）
- [[tools/claude-computer-use]]（ClaudeによるデスクトップUI自動操作機能）
- [[tools/claude-code-remote-control]]（スマホ・ブラウザから自分のPCのClaude Codeを遠隔操作する4レベル運用）
- [[tools/claude-code-github-repos]]（Claude Codeを強化するGitHubリポジトリ10選）
- [[tools/claude-code-subagents]]（サブエージェント：独立コンテキスト・Explore/Plan built-in・フォーク機能）
- [[concepts/claude-code-dynamic-workflows]]（Dynamic Workflows Research Preview：orchestrationスクリプト自動生成＋並列サブエージェントフリート）
- [[tools/hyperframes]]（Claude Codeから`/hyperframes`でHTML→動画を生成するOSSフレームワーク）
- [[concepts/claude-code-work-folder]]（初心者向け作業フォルダ設計：inbox/reference/draft/output/archive）
- [[tools/understand-anything]]（コードベースを対話型ナレッジグラフ化する補助ツール・Claude Code対応）
- [[concepts/claude-code-task-delegation]]（業務フローを渡す活用法50選と「環境を設計する」発想）
- [[concepts/product-role-archetypes]]（作者 Boris Cherny が Claude Code チームから抽出した5アーキタイプ＝Prototyper/Builder/Sweeper/Grower/Maintainer）
