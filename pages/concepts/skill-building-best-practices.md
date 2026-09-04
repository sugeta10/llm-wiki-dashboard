# スキル構築のベストプラクティス（Anthropic社内知見）

> **TL;DR**: 効果的なスキルは9カテゴリのどれか1つにきれいに収まる。複数を跨ぐスキルはエージェントを混乱させる。説明文は人間向けの要約ではなく「いつ起動するか」のトリガー定義として書く。

Anthropic社内では数百のスキルが実運用されており、その分類と書き方から「何が効くか」のパターンが抽出されている。スキルは「ただのMarkdownファイル」ではなく、スクリプト・アセット・データを含むフォルダであり、ファイルシステム全体をコンテキストエンジニアリングと[[concepts/spec-driven-development|progressive disclosure]]の道具として設計できる点が核心。良いスキルは数行と1つのgotchaから始まり、エージェントが新しいエッジケースに当たるたびに育てられていく。

## スキルの9カテゴリ（分類フレーム）

自社スキルライブラリの穴を見つけるための枠組み。1スキル＝1カテゴリが理想。

| # | カテゴリ | 中身 |
|---|---------|------|
| 1 | **Library/API reference** | 内部・共通ライブラリ/CLI/SDKの正しい使い方。参照コード片フォルダ＋gotcha一覧 |
| 2 | **Product verification** | コードが動くか検証する手順。playwright/tmux等と連携。社内で**最も品質インパクトが大きい**カテゴリ（エンジニアが1週間専念する価値あり） |
| 3 | **Data fetching/analysis** | データ・監視スタックへの接続。認証付き取得ライブラリ・ダッシュボードID・正規テーブル定義 |
| 4 | **Business process/team automation** | 反復ワークフローを1コマンド化。過去結果をログ保存して一貫性を担保（standup-post等） |
| 5 | **Code scaffolding/templates** | フレームワークのボイラープレート生成。自然言語要件がコードで覆えない部分に有効 |
| 6 | **Code quality/review** | コード品質強制・レビュー。決定論スクリプトやhook/GitHub Actionで自動実行（adversarial-review等） |
| 7 | **CI/CD and deployment** | fetch/push/deploy（babysit-pr・deploy-service・cherry-pick-prod） |
| 8 | **Runbooks** | 症状（Slackスレッド・アラート・エラー署名）→多ツール調査→構造化レポート |
| 9 | **Infrastructure operations** | 定常運用・破壊的操作。ガードレール付きでベストプラクティスを強制 |

## スキルを書くときの実践原則

- **当たり前を書くな**: Claudeは既にコードが書けコードベースも読める。デフォルト挙動の再記述は価値ゼロでコンテキストを食うだけ。知識系スキルはClaudeを「通常の思考の外」へ押し出す情報に集中する（frontend-designがInterフォント・紫グラデを避けさせるのが好例）
- **Gotchasセクションが最高シグナル**: 実際にClaudeがハマった失敗点を蓄積する。「subscriptionsテーブルはappend-only、欲しいのはcreated_at最新でなくversion最大の行」のような具体的落とし穴
- **ファイルシステムでprogressive disclosure**: SKILL.mdから`references/api.md`等を指し示し、必要時にClaudeが読む。詳細な関数シグネチャやテンプレートは別ファイルに分離
- **Claudeをレールに乗せ過ぎるな（railroading回避）**: スキルは再利用されるので過度に具体的な指示は危険。情報は与えつつ状況適応の余地を残す
- **セットアップを設計する**: ユーザー固有コンテキストが要るスキルは`config.json`に保存し、未設定ならエージェントがユーザーに尋ねる。構造化質問には`AskUserQuestion`ツールを使わせる
- **説明文はモデル向けに書く**: セッション開始時にClaudeは全スキルのdescriptionを走査して「このリクエストに合うスキルはあるか」を判定する。**descriptionは要約ではなくトリガー定義**。「babysit」のような起動語を含める
- **記憶を持たせる（Help Claude remember）**: append-onlyテキストログ・JSON・SQLiteでスキル内にデータを保存。`standup-post`がstandups.logを読めば前回からの差分を語れる。`${CLAUDE_PLUGIN_DATA}`で安定したデータディレクトリを取得
- **スクリプトを同梱しコード生成させる**: コードを与えるとClaudeはボイラープレート再構築でなく「次に何をするか」の合成にターンを使える。データ取得ヘルパー関数群を渡せば高度な分析スクリプトをその場で書ける
- **on-demandフック**: スキル呼び出し時のみ・セッション中だけ有効なフックを仕込める。`/careful`（rm -rf・DROP TABLE・force-push・kubectl deleteをPreToolUseでブロック）、`/freeze`（特定ディレクトリ外のEdit/Writeをブロック）など、常時onにすると鬱陶しいが時々極めて有用なものに使う

## 配布・マーケットプレイス運用

- **2つの共有方法**: ①リポジトリに`./.claude/skills`としてチェックイン（小チーム・少数リポ向け、ただし全スキルがモデルのコンテキストを少し食う）／②**プラグイン**化してマーケットプレイスで配布（スケール時。チームが選んでインストールでき、セットアップフローも持てる）
- **マーケットプレイスの選定はオーガニック**: 中央委員会で決めない。試したいスキルはGitHubのsandboxフォルダにアップしSlack等で告知→トラクションが付いたらPRでマーケットプレイスに昇格
- **スキルの合成（composing）**: 依存はまだネイティブ機能ではないが、スキル名で他スキルを参照すれば、インストール済みならモデルが呼び出す
- **計測**: PreToolUseフックでスキル使用をログ化し、人気/期待より過少起動のスキルを発見する

## 観察ログ（未検証）

- 2026-06-05: 著者はThariq Shihipar（Anthropic技術スタッフ、Claude Code担当）。「9カテゴリは決定版ではないが自社スキルライブラリの穴を見つけるのに有用なフレーム」と位置づけ
- 2026-06-05: 「verification系スキルが社内で最も計測可能な品質インパクトを持つ」というのはAnthropicのClaude Code運用に基づく主張（汎用的に成立するかは未検証）
- 2026-06-06: @shin_sasaki19 が同じAnthropic知見を日本語で再要約し「AI時代のナレッジマネジメント論として秀逸」と評価。「社内全スキルを棚卸しした結果、9カテゴリに分類可能」という核心が独立要約でも同じ形で抽出された（枠組みの妥当性を補強）

## 問い

- このwikiの操作（ingest/lint/review）は9カテゴリのどれに当たるか？ ingest=business process automation、lint/review=code quality に近い。1スキル1カテゴリ原則に照らすと現状のwiki-ingestは肥大していないか
- 「verification系が最高ROI」を自分のwikiに適用するなら、validate_wiki.py を中心にした検証スキルへの投資が効くか
- description＝トリガー定義の原則で、既存スキルのdescription起動精度を測れるか（[[concepts/claude-skills]]の損益分岐点の問いと接続）

## 関連

- [[concepts/claude-skills]] — Claudeスキルの基本概念（永続的職務定義・自動ロード）。本ページはその「作り方」側
- [[concepts/self-refining-skills]] — スキルに自己改善ループを組み込む設計（gotchas蓄積をLESSONS.mdで体系化したもの）
- [[tools/claude-code]] — スキルの実行基盤
- [[tools/claude-code-plugins]] — プラグイン/マーケットプレイス配布の実例カタログ
- [[tools/skill-cleaner]] — スキルの冗長・未使用・プロンプトバジェット超過を検出（計測の補助）
- [[design/ai-skills-design]] — デザイン職のSkillsとエージェントSkillsの交差
- [[tools/openclaw-agent-skills]] — 「1度書いて全所で再利用」を実装した共有スキルリポジトリの実例
- [[tools/ui-skills]] — UIエンジニア向け専門スキルカタログ（アクセシビリティ・アニメーション・Tailwind）の実例
- [[concepts/agent-skill-management-system]] — 本ページの「作り方」が前提とする、増加後の管理問題（発見・ライフサイクル・ガバナンス・合成・評価）
- [[concepts/parallel-e2e-testing]] — 「Product verification」カテゴリの実例。E2E作成スキルでflaky対策チェックリストを機械強制する実践
- [[concepts/agent-skill-eight-layer-design]] — 海外実務家による別の切り口（8層アーキテクチャ＋ループ強度3段階）。本ページの9カテゴリ分類と併用できる
- [[concepts/improver-skill-pattern]] — 「小さく保つ・progressive disclosure・スクリプト同梱」の原則を自己改善ループの文脈で裏付けるWarpの実践（Anthropic公式ブログ）
- [[concepts/instruction-patch-lifecycle]] — 本ページの「数行と1つのgotchaから育てる」を、モデル世代交代時に全部外して戻す「捨てる」工程と一つのサイクルにまとめた運用論（Boris Chernyの6か月リセット・説明文15,000文字上限・@kimuai08）
