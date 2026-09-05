# Claude Code オーケストレーション（並列運用）

> **TL;DR**: Claude Code が公式に持つ3つの並列の仕組み（subagent / agent teams / git worktree）を守備範囲で使い分け、役割は CLAUDE.md ではなく subagent 定義で固定し、人間は「コードを書く側」から「設計して任せる側」へ回るための運用フレーム。

1セッションに実装・テスト・設計議論・デバッグ履歴を全部詰め込むと、コンテキストの汚染・指示のコンフリクト・トークン肥大という構造的限界に当たる。これを解くのが「自分が指揮官になり、専門化した Claude を複数並べる」という発想。鍵は、雰囲気で役割を割るのではなく、Claude Code が**公式に用意した仕組みに正しく乗せる**こと。3つの仕組みは似て非なるもので、混同すると逆に事故る。

## 3つの並列の仕組み（守備範囲が違う）

| 仕組み | 性質 | 連携の形 | 隔離 |
|--------|------|---------|------|
| **subagent** | 同一セッション内の補助AI。調査・レビュー等の脇道タスクを独自コンテキスト・専用system prompt・限定ツール権限で実行し、**結果サマリーだけ**をメインに返す | メインにだけ報告。subagent同士は会話しない | コンテキストのみ |
| **agent teams** | 複数セッションがチームとして連携。lead が teammate を生成し、共有タスクリストと mailbox で連携 | **teammate同士が直接メッセージを送り合える**（subagentとの最大の差） | コンテキスト＋会話 |
| **git worktree** | 同じリポジトリから複数の作業ディレクトリを切り出す Git の機能。`claude --worktree feature-auth` で別ブランチ・別ディレクトリの隔離環境が立つ | ファイルを踏み合わない | **ファイルシステム** |

覚え方: **worktree がファイルの隔離を担当し、subagent と agent teams が作業そのものの連携を担当する**。worktree はあくまで Git のファイル分離なので、DB・環境変数・起動中サービスは共有されたまま — この境界の誤認が事故の元になる。

## 役割固定は CLAUDE.md でなく subagent 定義で

「CLAUDE.md に役割ID（arch / dev-main…）を書いて起動のたびにペルソナを切り替える」という説明が出回るが、これは仕組みとして存在しない。CLAUDE.md は context であって強制される設定ではなく、system prompt の後に user message として渡されるため厳密な遵守は保証されない（必ず止めたいことは [[concepts/claude-md-rules]] でなく hooks で縛る）。

役割の正本は **subagent 定義**（`.claude/agents/` 配下の Markdown）。name / description / tools / model を frontmatter に書けば再利用可能な専門家が1つ完成し、これは **agent teams の teammate としても名前指定で呼び出せる**（tools許可リストとモデルを引き継ぐ）。1回書けば subagent としてもチームメンバーとしても使い回せる、という設計。詳細は [[tools/claude-code-subagents]]。

## 記憶の共有は SQLite でなく公式 auto memory（markdown）

複数セッションをまたぐ記憶共有で「SQLite にタグ付きで記憶を貯めて動的注入する」テクニックが紹介されることがあるが、それは標準機能ではない（MCP経由でDBを組むなら可能、という別の話）。公式の auto memory は markdown ファイルで動き、`~/.claude/projects/<project>/memory/` に MEMORY.md（索引）と話題別ファイルを置き、Claude が作業しながら自分で書き溜める。毎セッション読まれるのは MEMORY.md の先頭分だけで、詳細は必要時に取りに行く設計なのでコンテキストをクリーンに保てる。**SQLite自作の前に、まず公式 markdown auto memory を使い切る**のが順序。

## 並列運用の落とし穴

- **トークンコスト**: agent teams は teammate ごとに独立コンテキストを持つため、使用量は人数に比例して増える。ルーチン作業を大人数でやるとコスト負けする
- **worktree の隔離範囲**: ファイル分離しかしないので、2セッションが同じローカルDBにマイグレーションをかけると壊れる。各worktreeで開発環境（依存・.env・Dockerコンテナ）を分けて初期化する
- **権限の継承**: teammate は spawn 時点で lead の権限設定を引き継ぐ。lead を `--dangerously-skip-permissions` で動かすと全 teammate も無確認状態になる。並列だと事故も並列に広がるので権限は lead 側で絞る

この「複数の仕組みが守備範囲を分担しつつ組み合わさる」構図は、[[concepts/multi-agent-patterns]] の Orchestrator-subagent / Agent Teams パターン、[[concepts/claude-code-large-codebase]] のハーネス設計とも地続き。

## 観察ログ（未検証）

- 2026-06-05 (@nobel_824): 著者の実運用は**指揮・実装・品質の3層に8セッション**を配置する構成。指揮層2（PM/Architect）・実装層4（Frontend/Backend/Infra/Security）・品質層2（QA/Reviewer/Technical Writer）。実装層は worktree で隔離、品質層は subagent 定義／teammate で固定、各層は共有 Markdown ファイルを介して情報をリレー（単一ソースの設計例）
- 2026-06-05: agent team の1チームは**3〜5人が公式の目安**、teammate あたり5〜6タスクとされる。「3人の集中したメンバーは5人の散漫なメンバーに勝つことが多い」という公式の記述を引用。8人を1チームに詰めず、3〜5人チーム＋worktree＋subagentで合計8にするのが安全という著者の解釈
- 2026-06-05: agent teams は実験的機能でデフォルト無効。`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` を設定し v2.1.32 以降が必要、と著者が公式記述として紹介（未確認のバージョン情報）
- 2026-06-05: auto memory は v2.1.59 以降で使え、リポジトリ単位保存で worktree 間共有・マシンローカル、と著者が公式記述として紹介（未確認のバージョン情報）
- 2026-06-05: 「作る人」と「疑う人」を別teammateに分ける構成（Architect-Reviewer）の効きが大きい、という著者の主張。公式の「競合する仮説」例（5人の teammate にバグの別仮説を割り当て反証し合わせ、生き残った説を真因とみなす）を引用 → [[concepts/llm-council]] の匿名ピアレビューと発想が近い
- 2026-06-05: teammate に plan approval を要求すると読み取り専用 plan mode で設計だけ出し、lead 承認まで実装に入らない。agent teams には TeammateIdle / TaskCreated / TaskCompleted フックがあり exit code 2 でフィードバックを返して継続させられる、と紹介（「テストが緑でないと完了させない」をコードで縛る用途）

## 問い

- 自分の obsidian-vault 運用（[[concepts/obsidian-personal-os]]）に agent teams を入れる価値はあるか、それとも subagent + worktree で足りるか
- 「3〜5人が最適・3人の集中が5人に勝つ」は自分のタスクでも再現するか。人数を増やしてスループットが逓減し始める点はどこか
- 役割を subagent 定義に固定する vs CLAUDE.md に書く、の精度差を自分のワークフローで測れるか（[[concepts/claude-md-rules]] の効きとの比較）

## 関連

- [[tools/claude-code-subagents]] — subagent定義の正本（`.claude/agents/`・CLAUDE_CODE_FORK_SUBAGENT）
- [[concepts/multi-agent-patterns]] — マルチエージェント設計パターン全般（Orchestrator-subagent/Agent Teams）
- [[concepts/claude-code-large-codebase]] — 大規模コードベースでのハーネス設計・自己改善
- [[concepts/claude-md-rules]] — CLAUDE.md行動ルール（強制でなく強めの推奨という位置づけ）
- [[tools/claude-code]] — Claude Code本体
- [[tools/claude-code-remote-control]] — 複数セッションを常駐窓口から立ち上げて出先から操舵するリモート運用（「仮想社員」運用の入口）
- [[concepts/coding-agent-workflow-styles]] — 並列運用は高速制御／委譲低速の2類型のうち委譲側に寄る選択肢
- [[tools/html-share]] — 並列数を増やしたあとの状況把握側。作者minorun365は8並列で稼働させたまま外出し、成果物をスマホから追う運用を報告している
- [[tools/herdr]] — 「自分が指揮官になる」フレームの指揮官役をメインエージェントに渡し、Claude と Codex の各ペインへ仕事を割り当てて結果を読ませるマルチペイン運用（@Voxyz_ai）。クォータの速い Fable 5.1 を方向づけ・分解・サインオフに限定する配分も同ページ
