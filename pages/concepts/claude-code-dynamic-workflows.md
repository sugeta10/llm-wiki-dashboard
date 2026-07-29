# Claude Code Dynamic Workflows

> **TL;DR**: Claudeがタスクごとに使い捨てのオーケストレーション（JavaScriptハーネス）をその場で書き、独立コンテキストを持つサブエージェント群に分散・協調実行させる [[tools/claude-code]] の Research Preview 機能。

デフォルトの Claude Code ハーネスはコーディング向けに作られているが、1つのコンテキストウィンドウで「計画」と「実行」を同時に抱える設計のため、長時間・大規模並列・高度に構造化された敵対的タスクでは破綻しやすい。Dynamic Workflows は、それぞれが独立したコンテキストと焦点を絞ったゴールを持つ複数の Claude をオーケストレーションすることで、この限界を構造的に回避する。プロンプトに「workflow」という単語を含めるか、トリガーワード「ultracode」を使うと起動する。

## 解こうとしている問題（単一コンテキストの3つの失敗モード）

長時間1つのコンテキストで複雑タスクを回すと、Claudeは以下に陥りやすい。Dynamic Workflowsはコンテキストを分割することでこれらを構造的に防ぐ。

- **Agentic laziness（怠惰）** — 多段タスクの途中で「終わった」と宣言してしまう（例: セキュリティレビュー50項目中20項目で完了扱い）
- **Self-preferential bias（自己選好バイアス）** — 自分の出力・発見を好み、自分でルーブリックに照らして検証・判定すると甘くなる
- **Goal drift（ゴールの漂流）** — ターンを重ねるごと、特にcompaction（要約）後に元の目的への忠実度が失われる。要約は不可逆に情報を落とすため「Xするな」等の制約やエッジケース要件が消える

## 仕組み

- 数個の特殊関数（サブエージェントの起動・協調用）を持つ JavaScript ファイルを実行する
- `JSON` / `Math` / `Array` 等の標準JS関数も使え、データ加工ができる
- 各エージェントが使うモデル（Sonnet/Opus）やworktree隔離の有無をworkflow自身が決められる → 必要な知能レベルと隔離を選択できる
- ユーザー操作やターミナル終了で中断しても、セッション再開で途中から再開できる
- 1セッション内で数十〜数百の並列サブエージェントを動かせる

## Dynamic vs Static workflows

従来は Claude Agent SDK や `claude -p` で複数 Claude Code を協調させる**静的**workflowを作れた。だが静的workflowは全エッジケースに対応する必要があり汎用的になりがち。[[models/claude-opus-4-8]] と dynamic workflows では、Claude が用途に合わせた**カスタムハーネスをその都度書く**ほど賢くなった点が差分。

## 主要パターン（組み合わせて使う）

- **Classify-and-act** — 分類エージェントでタスク種別を判定し、種別ごとに別エージェント/挙動へルーティング（末尾に置いて出力を決める使い方も）
- **Fan-out-and-synthesize** — タスクを多数の小ステップに分割し各々をエージェントで処理、最後に統合。synthesizeステップはバリア（全fan-outを待ってから構造化出力をマージ）。各ステップがクリーンなコンテキストを持つため相互汚染しない
- **Adversarial verification** — 生成エージェントごとに別の検証エージェントを立て、ルーブリックに照らして**敵対的に**出力を検証する
- **Generate-and-filter** — アイデアを多数生成→ルーブリック/検証でフィルタ・重複除去し、最高品質のものだけ返す
- **Tournament** — 分業ではなく競争。N個のエージェントが異なるアプローチで同じタスクに挑み、判定エージェントがペアワイズ比較で勝者を決める（絶対スコアより比較判断の方が信頼できる）
- **Loop until done** — 作業量が未知のタスクで、停止条件（新発見なし・ログにエラーなし）を満たすまでエージェント起動を繰り返す。固定回数passより末端を取りこぼさない

→ Anthropic公式の [[concepts/multi-agent-patterns]] の Fan-Out / Orchestrator-subagent をユーザー向けに抽象化・実行可能化したもの。

## 使い方

1. `/config` の **Dynamic workflows** が `true` になっていることを確認する
2. プロンプトに「workflow」という単語を含めて指示を出す（または「ultracode」でworkflow生成を強制）
3. Claude が動的にオーケストレーションプランを作成し厳密に実行する — 各ステージが正しい順序で処理される

## ユースケース（非技術タスクほど有効なことも多い）

- **移行・リファクタ** — callsite/失敗テスト/モジュール等の単位に分解し、各fixをworktreeでサブエージェントが直し→別エージェントが敵対的レビュー→マージ。BunのZig→Rust書き換えはworkflowで実施された
- **Deep research** — `/deep-research` スキルがdynamic workflowで実装（web検索をfan-out→ソース取得→主張を敵対的検証→引用付きレポート合成）。Web以外にSlack文脈やコードベース探索にも応用可
- **Deep verification** — レポート内の全事実主張を1エージェントが洗い出し、各々を別サブエージェントが詳細検証。ソースの品質まで検証エージェントでチェック
- **Sorting** — 1000行超を1プロンプトでソートすると品質劣化＆コンテキスト溢れ。代わりにトーナメント/ペアワイズ比較パイプライン/並列バケットランク。各比較が独立エージェントで、決定論ループがブラケットを保持
- **Memory / ルール遵守** — Claudeが見落とすルールを検証エージェント（1ルール1検証）でチェック。逆に過去セッション・レビューコメントから繰り返す修正を採掘→クラスタ化→敵対的検証（このルールが実際のミスを防げたか）→CLAUDE.mdに蒸留。skepticペルソナで偽陽性を抑制
- **Root-cause investigation** — logs/files/dataなど互いに素な証拠から独立仮説を生成するエージェントを立て、各仮説を検証者・反証者のパネルにかける（自己選好バイアスを構造的に排除）。コード以外に売上分析・データ基盤post-mortemにも
- **Triage at scale** — サポートキュー/バグ報告を分類→既存トラッキングとdedupe→対応。**quarantine**パターン（信頼できない公開コンテンツを読むエージェントには高権限アクションを禁じ、行動は別エージェントが担う）。`/loop` と組むと継続実行
- **Exploration and taste** — デザイン・命名などtaste依存の探索。ルーブリックを持つレビューエージェントが基準を満たすまで
- **Evals** — worktreeでエージェントを分離起動し、比較エージェントがルーブリックに照らして採点（スキルの評価・改善など）
- **Model / intelligence routing** — 分類エージェントが事前リサーチしてタスク複雑度を見積もり、Sonnet/Opusを選択する

## いつ使わないか・Tips

- **workflowは万能ではなく、トークンを大幅に多く消費する**。通常のコーディングタスクには不要なことが多い（「本当に追加の計算が必要か？」を問う。大半は5人レビューパネルなど要らない）
- 大タスク専用ではない。「quick workflow」（前提の敵対的レビュー等）も有効
- `/loop`（定期実行）・`/goal`（ハードな完了要件）と組み合わせると、triage/research/verificationが強化される
- **トークン予算**を明示できる（「use 10k tokens」でキャップ設定）
- workflow menuで「s」を押すと保存でき、`~/.claude/workflows` に置くかスキルで配布できる。スキルで配布する場合はJSファイルをスキルフォルダに入れSKILL.mdで参照する。「verbatim実行するスクリプト」でなく「テンプレート」として扱うようClaudeに促すと柔軟になる

## 検証済み事実

- 2026-06-02: Anthropic Claude Codeチームの Thariq Shihipar・Sid Bidasaria が公式記事で、dynamic workflowsがリリース済みであり、上記6パターン・3失敗モード・ユースケースを公式に解説した（Claude Blogにも掲載）
- 2026-06-02: BunのZig→Rust書き換えがworkflowを用いて実行されたと公式記事が明言

## 観察ログ（未検証）

- 2026-06-02: 著者いわく「workflowはベストプラクティスがまだ発展途上」「非技術タスクの方がむしろ有用なことが多い」「many tasks resemble coding tasks（多くのタスクはコーディングタスクに似ている）」
- 2026-06-03: @0xCodez が同ローンチ内容を「6パターン・14ステップ」として再構成した二次まとめを投稿（X bookmark 2,715 / 1,118,716 imp、2026-06-05時点）。中核APIを `agent()` / `parallel()` / `pipeline()` の3関数と整理し、「`parallel()` はバリア（全結果を待つ）、`pipeline()` はストリーミング（各itemが独立に各stageを流れ、安く速い）」と対比。判断基準は「次に進む前に全結果が要るか？ Yes→parallel / No→pipeline」。新情報は本文に既出のため観察ログのみに記録
- 2026-06-04: @omarsar0（Elvis Saravia / DAIR.AI）が「Dynamic Workflowsにハマっている。ハーネスをその場で生成する発想が魅力的で、自分のエージェントオーケストレータ向けにリバースエンジニアリングした」と投稿。さらにタスク・メトリクス・レポートを追うモニタリングダッシュボードをHTML artifactとして自作（X bookmark 1,563 / 2026-06-05時点）。Claude Code固有機能が外部のエージェント基盤へ移植・模倣され始めた事例

## 問い

- 自分のwikiシステムのingest/lint/reviewにworkflowは効くか？特にlint（孤立ページ・壊れたリンク検出を並列化）やreview（Tier判定パターン採掘）は fan-out / loop-until-done と相性が良さそう
- トークン2〜4倍のコストに見合うのはどのタスクか。「本当に追加計算が必要か」の判断基準を自分のユースケースで持てるか
- [[concepts/eval-loop]] の品質ゲートを workflow の adversarial verification / generate-and-filter で実装できるか

## 関連

- [[tools/claude-code]]
- [[concepts/multi-agent-patterns]] — 本機能が抽象化した元のパターン集
- [[concepts/claude-code-hooks-async]]
- [[tools/claude-code-subagents]]
- [[models/claude-opus-4-8]]（Opus 4.8でのDynamic Workflows設定・コスト目安）
- [[concepts/eval-loop]] — generate-and-filter / adversarial verification の品質ゲート応用
- [[tools/claude-code-goal]] — `/goal` との組み合わせ（ハード完了要件）
- [[concepts/claude-code-loop-types]] — 公式4類型。Proactive ループの合成部品として本機能を位置づける
- [[concepts/graph-engineering]] — 本機能をノード/エッジ/契約というグラフ語彙で捉え直し、バリアの要否を判定するスメルテストまで踏み込んだ実践論（@0xCodez）
- [[tools/claude-code-ultracode]] — トリガーワード「ultracode」自体の仕様（最大16並行・合計1000体上限）と`/goal`との組み合わせ方の解説
