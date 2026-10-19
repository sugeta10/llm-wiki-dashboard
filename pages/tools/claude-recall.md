# claude-recall：Claude Code の全セッションを SQLite に残し、人と Claude の双方が引ける recall ツール

> **TL;DR**: Claude Code が書く JSONL のセッション記録を SQLite の書庫（`~/.claude/vault.db`）に取り込み続け、Claude Code が消した後も残したうえで、MCP サーバー（Claude が過去セッションを検索・読解）・TUI（自分で探して再開）・Web UI（ブラウザで読む）の3経路で引ける1バイナリ `recall`。記憶システムでなく「必要なときに引く（pull）」道具だと作者 babarot が明言し、作者と考えられる @b4b4r07 は日次ダッシュボードとハーネス自己改善ループの基盤に使っていると投稿した。

Claude Code のセッション記録は `~/.claude/projects/**/*.jsonl` に書かれるが、Claude Code 自身が後で消す。claude-recall の README は目的を「その grep をもっと良い体験にし、Claude Code が消した後もファイルを残すこと」と書き、自分かエージェントが「前にも話した」と気づいたときに引く道具だと位置づける（設計判断 ADR-001「recall であって memory の拡張ではない」）。作者 babarot は、X の @b4b4r07（GitHub の b4b4r07 アカウントの表示名も BABAROT）と同一人物と考えられる。@b4b4r07 は、recall が最近「自分が作るものの基盤」になってきており、一日を振り返るダッシュボードに繋いで Claude Code の中でその日何をしていたかを表示させたり、agent harness の自律改善ループに繋いで**人間の介入パターンからハーネスの修正案を自動で作る**ようにしたりしていると投稿した。@shodaiiiiii は「Claude Code のセッションを SQLite に溜めておき、過去の会話を自分も Claude も思い出せるようにするツール」という紹介文（引用元の投稿者はキャッシュに残っていない）を引いて「そんなやり方があったか」と反応している。

```mermaid
flowchart LR
  J["~/.claude/projects/**/*.jsonl<br/>（Claude Code が書き、後で消す）"] -->|recall import<br/>SessionEnd hook| DB[("~/.claude/vault.db<br/>SQLite + FTS5")]
  DB --> MCP["MCP サーバー<br/>recall_search / list / export / stats"]
  DB --> TUI["TUI<br/>探す→claude -r で再開"]
  DB --> WEB["Web UI<br/>ブラウザで読む・ライブ更新"]
  MCP --> CC["作業中の Claude Code セッション"]
  DB -.->|@b4b4r07 の用途| DASH["日次ダッシュボード"]
  DB -.->|@b4b4r07 の用途| LOOP["ハーネス自己改善ループ<br/>介入パターン→修正案"]
```

## 3つの入口と Claude Code 内の表示（README からの転記）

| したいこと | 使うもの | それだけが与えるもの |
| --- | --- | --- |
| 過去セッションで何があったかを Claude に思い出させる | MCP サーバー | 答えが作業中のセッションに、すぐ使える文脈として届く |
| 自分でセッションを探して戻る | TUI | その場で再開（`claude -r`）、新しい claude で recall、ID をコピーして別エージェントに渡す |
| 自分で探して読む | Web UI | 長い会話と画像のための読みやすいリーダー |
| セッションを離れずに振り返る | Claude Code 内 | 起動時に当該リポジトリの直近セッション、モデルのターン無しの検索、1キーで任意セッションの recap |

- **MCP サーバー**: 各 Claude Code セッションが自分の `recall mcp` を起動する。プロンプトには何も注入されず、Claude は過去について聞かれたとき、または前に話したと気づいたときにツールを呼ぶ。ツールは `recall_search`（全文検索・`repo` で1リポジトリに絞れる）・`recall_list`・`recall_export`（`tail` で末尾だけ）・`recall_stats`。プロンプト `recap` は、あるセッションの終わり方を読み、やったこと・決めたこと・残したことをまとめ、どう続けるかを尋ねる
- **TUI**: タイトル・フォルダ（worktree は属するリポジトリの下）・ブランチ・メッセージ数・サイズ・ID を一覧。`Enter` で再開、`c` で再開できないセッション（worktree が消えたもの等）を新しい claude から MCP 経由で recall、`/` でフィルタ（`text:`・`title:`・`folder:` で項目指定）、`a` で「何の話だったかは覚えているが何を打てばよいか分からない」ときに自分の `claude -p` で探させる（API キー不要）
- **Web UI**: `recall ui` で `localhost:6276` にバックグラウンド起動。ツール呼び出しを畳んだリーダー・自分の発言のアウトライン・会話内検索。新規・変更セッションは約0.5秒で取り込まれライブで入る。待ち受けは 127.0.0.1 のみ
- **Claude Code 内**: プラグインに同梱の Claude Code mod（hooks module）が、起動時に同じリポジトリ（worktree 含む）の直近セッションをプロンプト上の帯に出し、`recall_search` の結果をリポジトリ→セッション→一致メッセージの木で描き、`/recall <query>` でモデルのターン無しに検索して表で出す。`/recall <n>` やセッション ID の押下で `recap` が走る。hooks module を読む Claude Code が要る（2.1.291 で試験）

## 記憶システムではない（claude-mem との比較・README からの転記）

README は claude-mem を「関連する問題を解く優れたプロジェクト」と認めたうえで、意図的に別の問題を解くと書く。

| | claude-mem | claude-recall |
| --- | --- | --- |
| 目的 | エージェントの記憶を拡張する | 人とエージェントが思い出すのを助ける |
| 注入 | Push（`SessionStart` で自動注入） | Pull（必要なときに引く） |
| 保存するもの | LLM が要約した観察 | ノイズを除いた生の会話 |
| 検索 | FTS5＋Chroma ベクトルのハイブリッド | FTS5 のみ（決定論的） |
| LLM 呼び出し | 索引作成時（ホスト型 observer・自分の OpenRouter/Gemini キー・Anthropic プラン） | 保存・検索では呼ばない。TUI の `a` で自分の `claude` を使うときだけ |
| データの置き場 | ローカル＋任意でクラウド同期 | ローカルのみ |
| 対応エージェント | Claude Code・Codex・Gemini・OpenCode ほか | Claude Code |
| ランタイム | Node＋Bun＋Python（uv）＋Chroma・常駐ワーカー | 静的バイナリ1つ・デーモン無し |

claude-recall が選ぶトレードオフとして README は、生ログは drift しない（保存されるのは起きたことであって要約ではない）・エージェントは知らないときに知らないと言う（過去の文脈はツール呼び出しから来るので誤って思い出すことがない）・待機中は何もしない（ワーカー無し・背景の LLM 呼び出し無し）・1バイナリでオフライン動作、の4点を挙げる。

## 導入と書庫

- **インストール**: curl のインストーラ（`~/.local/bin` に `recall` を置き、セッションを取り込み、`claude` が PATH にあれば MCP サーバーを登録）、Nix（babarot/nur-packages）、ソースからのビルド（Go・Node.js・mise。`go install` でも可だが Web UI は含まれない）
- **Claude Code プラグイン**（推奨の接続方法）: MCP サーバー（`recall mcp`）・`SessionEnd` hook（`recall import`）・hooks module（Claude Code 内の表示と `/recall`）を束ねる。hooks module を読まない古い Claude Code でも MCP サーバーと hook は使える。`~/.claude/skills/` 下のプラグインディレクトリは `claude-recall@skills-dir` として読み込まれる
- **手動設定**: `recall import` で `~/.claude/projects` から書庫を作り、`claude mcp add claude-recall -s user -- recall mcp` で接続。MCP サーバーと Web UI は起動時に書庫を作って取り込むので、MCP を繋いだ最初のセッションでも取り込みが走る
- **書庫**: `~/.claude/vault.db`。Claude Code が消した JSONL のセッションは書庫にしか残らないので、バックアップし、作り直すために消さないこと（`sqlite3 ~/.claude/vault.db ".backup '...'"`）。`CLAUDE_CONFIG_DIR` や設定の `extra_projects_dirs`（コンテナのツリーなど）からも取り込む。保存するのはユーザー・アシスタントのテキスト、thinking、ツール呼び出しと結果、スラッシュコマンド展開とタスク通知。除外はシステムイベント、ファイル履歴スナップショット、サイドチェーン、進行・キュー操作などの帳簿
- **設定**: `~/.config/claude-recall/config.toml`（`[core] db`・`[ui] port`・`[tui] theme / detail_position`・`[keys]`）。誤りは行番号つきで報告され、無視されない
- **更新**: `recall update`（curl 導入のみ。1.7.2 より後のリリースで入ったので、それ以前の導入はインストーラを一度再実行）
- **技術**: Go・modernc.org/sqlite（CGo 無しの SQLite・FTS5）・Bubble Tea（TUI）・MCP Go SDK・Preact/Vite/Tailwind（Web UI）。Deno から Go へ移植した経緯は ADR-004

## 作者が解く問題：会話にしか残らないことを grep でなく DB で引く（Zenn 記事より）

作者 babarot は Zenn の解説記事で、思い出したくなる場面を3つ挙げる。設計相談で2案のうち片方に決めたが、コードには採った方しか残らないので採らなかった方をなぜやめたか知りたい。障害調査で Claude とログやクエリを見て原因を突き止め、似た症状が再発したときにどこを見て何を試し何が外れだったか知りたい。別セッションで今の作業に関わる方針を話していて、そのセッションに戻らず今の Claude にその話を踏まえてほしい。どれもコードや git log に残らず会話の中にしかない、と書く。

transcript は `~/.claude/projects/` 下の JSONL なので、Claude に「前のセッションを grep して」と頼めば読める。だが作者は、当たりはしても目当てのセッションに辿り着くまで手数がかかる、材料が grep 向きではないからだと書く。1行が1つの JSON で、会話のほかに進捗やファイル履歴のスナップショットまで入っており、ヒット行を読むと JSON の塊でコンテキストが埋まる。ファイル名はセッション ID でタイトルが無く、どのファイルがどのセッションか中を開かないと分からず、worktree だとフォルダ名から元リポジトリを辿るのも難しい。そこで先に読みやすい形に直して DB に入れ、Claude にはそこを検索させる。DB に残すのはユーザーとアシスタントのテキスト、thinking、ツール呼び出しと結果で、進捗などの記録は捨てる。これで**元の JSONL の7%ほど**の大きさになると作者は書く。セッションにはタイトルとどのリポジトリ（worktree 含む）のものかを持たせ、Claude は「どのリポジトリの・どのセッションの・どのメッセージに当たったか」を受け取り、読みたいセッションだけを、必要なら末尾だけ書き出して読む。

もう1つの理由は transcript がいずれ消えること。何も設定しなければ Claude Code は30日で JSONL を消す（`cleanupPeriodDays`）。設定で延ばしても JSONL が残るだけで探しにくさは変わらず、DB に取り込んでおけば消えた後のセッションも残り続ける（そのとき DB が唯一のコピーになる）。取り込みはセッション終了後でなく会話が進むそばから行われ、transcript が書き換わると数百ミリ秒で DB に入るので、別セッションで今まさに話していることもすぐ検索できる。

## claude-mem を選ばなかった理由と、引き換えに受け入れた限界（作者の弁）

作者は最初に claude-mem を試し、便利そうではあるが自分には合わなかったと書く。Claude Code は毎回まっさらな状態で起動したい。新しいセッションには今の作業のことだけを渡し、前のことは思い出してほしいときにだけ思い出してほしい。勝手に入ってくる要約は今の作業に関係なくても Claude の前提になり、どこまでがこのセッションで見たことでどこからが注入された記憶かが自分から見えにくく、要約が原文からずれることもある。だから claude-recall は Claude の記憶を作ったり延ばしたりせず、やるのは「transcript を DB に取り込む」「頼まれたら DB を検索して見せる」の2つだけにした。LLM で要約せずプロンプトにも注入しない。Claude は「わからないので調べます」と言って調べ、読んだ原文を根拠に答える。記事の比較表は README の表（上の節）の5行版（目的・文脈の入り方・保存するもの・検索・常駐）で、中身は同じ。

引き換えにできないこととして作者は2つ挙げる。ベクトル検索をしないので、過去セッションで使っていない言葉では引っかからない（「認証エラー」で探しても「401」としか書いていないセッションは出てこない）。Claude が「前に話したかもしれない」と気づかなければ recall は呼ばれない。後者は MCP のツール説明に「過去の議論や決定を探すときに使う」と書いてあるのである程度は Claude から呼び、確実に使わせたいなら CLAUDE.md に一言書く手もある。作者は「どちらの限界も、勝手に注入しないことと引き換えに受け入れている」と書く。

## TUI と Web UI の補足（Zenn 記事より）

- **`a`（TUI から Claude に探させる）**: 自分で絞り込もうとして言葉が出てこないとき（「確か Nix のパッケージの話をしていた気がする」程度の記憶）に質問を書くと、Claude が recall で探して見つけたセッションを TUI の一覧に返す。中では `claude -p` を recall の検索と一覧のツールだけ許して動かす

  ```
  claude -p <question> --model <ask_model> --no-session-persistence \
    --strict-mcp-config --mcp-config <recall mcp> \
    --tools "" --allowedTools mcp__recall__recall_search,mcp__recall__recall_list
  ```

  自分のログインで動く claude を呼ぶので API キーも claude-recall 側の設定も要らない。答えには見つかったセッションと選んだ理由が並び、1つに飛ぶことも、見つかったセッションだけに一覧を絞ることもできる。作者は、claude-recall が LLM を呼ぶのはこの `a` と `c` で人間が頼んだときだけと書く
- **`c`（resume できないセッションの擬似再開）**: `claude -r` で戻れないのは、worktree を消した後のセッション（フォルダが無い）と Claude Code が transcript を消したセッション。worktree を作っては消す使い方だとこれが多いと作者は書く。`c` で新しい claude を立ち上げ、recall の MCP でそのセッションを擬似的に再開する。最初の欄に「リトライの方針について」のように話題を入れればそこを、空なら「どこまでやったか」を思い出して指示を待つ。TUI はフォルダの有無だけでなく transcript が残っているかも確かめ、消えていればフッターの resume に取り消し線を引き、詳細ペインのサイズの横に deleted と出す。`Enter` を押すと transcript が消えていることと `c` で再開できることを伝える
- **その他のキー**: `Space` で下のペインに会話を広げて読み、`/` で会話の中を検索。`y` でセッション ID をコピー（別のエージェントに「このセッションを recall で見て」と渡す用）。`?` でキーのヘルプ
- **Web UI との分担**: TUI は戻れるのが強み、Web UI は長い会話や画像を読みやすいのが強み。動かしている間は新しいメッセージが半秒ほどで反映されるので、動いているセッションを横で眺められる。セッションの「やったこと」ビューに、使ったツール・走らせたコマンド・編集したファイルがまとまる

## 仕組み（Zenn 記事からの転記）

### watcher と importer

transcript の変化は watcher が見つけ、importer が DB に取り込む。watcher は fsnotify を使わずポーリングする。作者の理由は、macOS の fsnotify（kqueue）は transcript ごとにファイルディスクリプタを開く必要がありディレクトリを再帰的に監視できないこと、サイズと mtime を見るだけなら transcript が1000個あっても1回の走査は数ミリ秒で済むこと。変化を見つけても書き込みが 300ms 止まるまで待ってから取り込むので、Claude が続けて書いても import は1回にまとまる。

- 前回取り込んだときと mtime が同じファイルは読まない
- 残すもの: ユーザーとアシスタントのテキスト、thinking、ツールの呼び出しと結果、スラッシュコマンドの展開。捨てるもの: 進捗、ファイル履歴のスナップショットなどの記録
- 1セッションを1トランザクションで、JSONL の今の中身に丸ごと置き換える。メッセージは `(session_id, uuid, block_index)` が一意キーなので、何度取り込んでも結果は変わらない
- 同じ DB に書き込むプロセスは複数ある。書き込む接続は `BEGIN IMMEDIATE` で最初に書き込みロックを取り、`busy_timeout` で最大60秒待つので、同時に import しても失敗せず順番待ちになる。watcher と SessionEnd hook が同じセッションを同時に取り込むこともあるので、ロックを取った後にファイルが読んだときのままかを確かめ、変わっていれば読み直してから書く。先に読んだ方が後からコミットして古い中身で上書きすることはない
- SQLite は WAL ファイルを自分では縮めないので、`journal_size_limit` でリセットのたびに 64MB まで切り詰める

### 誰が watcher になるか（リーダー選出）

Claude Code はセッションごとに `recall mcp` を1つ立ち上げる。セッションを10個開いていれば `recall mcp` も10個動き、全部が watcher を回すと同じ変更を10回取り込み CPU もセッションの数だけ使う。そこで watcher は1つだけにし、DB の隣のロックファイル（`vault.db.watch-<hash>.lock`）で決める。作者は Kubernetes の Lease と同じく、持ち主が定期的に更新し一定時間更新されなければ失効したとみなす形だと書く。

- `flock(LOCK_EX|LOCK_NB)` でロックを取れた1つが watcher になる。ロックはプロセスが終われば OS が外すので、落ちても残らない
- ほかは5秒ごとにロックを取りにいき、取れたら watcher を引き継ぐ。引き継いだときは、誰も見ていなかった間に変わったものを先にまとめて取り込む
- watcher は1秒ごとにロックファイルの mtime を更新する
- ロックが取られたまま mtime が1分止まっていたら、持ち主は止まっているとみなす（claude を Ctrl-Z で止めるとその `recall mcp` も止まる）。待っている1つが次の枠（`.lock.1`）のロックを取って watcher になる
- watcher は mtime を更新するたびに、自分より後ろの枠で動いている watcher がいないかを確かめ、いれば watcher をやめて待つ側に回る

最後の決まりは Mac のスリープのためにある。スリープ中は全員が止まるので、起きたときにはどのロックファイルも1分以上更新されていないように見え、待っていた1つが次の枠で watcher になる。元の持ち主は止まっていたわけではないので、そのままだと watcher が2つになり、スリープのたびに増えていく。ロックファイル名の `<hash>` は読むツリーの組から作り、`CLAUDE_CONFIG_DIR` や `extra_projects_dirs` が違うプロセスはそれぞれ別の watcher を持つ。Web UI はこれとは別に、ライブ更新のため自分でも watcher を回す。

### MCP サーバと検索

MCP サーバは公式の Go SDK で書かれ stdio で Claude Code とやりとりする。起動するとすぐ応答し、watcher になったときの追いつきの import はバックグラウンドで実行する。Claude Code は MCP サーバの起動を30秒しか待たないので、import が終わるまで応答しないと MCP サーバが失敗したことになる（アップグレード直後の最初の import は時間がかかる）。import が失敗してもサーバは落ちない。検索は SQLite の FTS5 で、tokenizer は空白で単語を区切るため日本語の文は1つのトークンになり文中の単語では引っかからない。空白のない語で検索されたときは部分一致で探す。

### どの入り口も同じ答えを返す

セッションのタイトルや、どのリポジトリのどの worktree のセッションかは Go 側の1か所で決め、TUI・CLI・MCP サーバ・Web UI はどれもそれを通して読む。TUI に出るタイトルと Claude が `recall_list` で受け取るタイトルは食い違わない。plugin の mod も同じで、描くだけで判断はしない。mod に必要なものは Go 側で CLI と MCP の JSON に足してから使う。検索結果のリポジトリ・タイトル・サイズ、日本語の部分一致、そして `recallOnly`（recall の MCP ツールしか呼んでいない、振り返っただけのセッションの印。そういうセッションは同じ語で検索するたびにヒットして実際に作業したセッションを押しのけるので、mod では1行にまとめる。ID は押せる）。こうしておけば plugin を入れず MCP だけで使う人にも同じ情報が届く。常駐するプロセスは無く、MCP サーバは Claude Code が立ち上げ、Web UI は `recall ui` で起動したときだけ動く。

## 他ページとのつながり（wiki 側の推論）

- [[concepts/ai-session-handover]] は、セッションの終わりに引き継ぎノートを書き出して次に渡す push 型の引き継ぎ。claude-recall の `recap` prompt と TUI の `c` は、書き出しを省いて生の transcript から「やったこと・決めたこと・残っていること」を後から組み立てる pull 型で、ノートを書き忘れたセッションや worktree を消したセッションにも効く点で補完関係と考えられる
- [[tools/artifact-share]]（coji）の preview も常駐デーモンを持たない設計で、複数エージェントを起こす合図をエージェントごとに作り分けた。claude-recall はセッションごとに立つ `recall mcp` 同士を flock のリーダー選出で1つの watcher にまとめる。「常駐させない代わりに、複数プロセスの調停をどこで引き受けるか」が両者の設計の分かれ目と考えられる
- [[concepts/skills-over-memory]]（@mvanhorn）が言う「push と pull を分け、recall を残したいなら pull 側へ移す」を、claude-recall はツールの設計方針として明文化している。README の比較表の「注入: Push / Pull」の行がそのまま対応する
- [[tools/claude-code-obsidian-sync]]（ペパボ）は同じ JSONL を Obsidian の Markdown に落とす。claude-recall は Markdown 化せず SQLite に生のまま溜めて検索と再開に使う点が異なり、@b4b4r07 の日次ダッシュボードは [[concepts/computer-history-daily-note]] の「機械が事実を集め、人が振り返る」型を Claude Code の作業に限って実装したものと読める
- 「人間の介入パターンからハーネスの修正案を自動で作る」は、[[concepts/correction-routing]] の「訂正を共有知へ書き戻す」ループや [[concepts/improver-skill-pattern]] の observer skill を、セッション書庫を入力にして回す形と考えられる。投稿は用途を述べるのみで、介入の検出方法や修正案の精度は示されていない
- 名前が同じ [[tools/pstack]] の `/recall` スキルは、チャット履歴と共有記録からトピックの現状ブリーフを再構築する別物である。@poteto の [[concepts/agent-trust-inverse-pyramid]] の手順2「`/recall` で過去のトランスクリプトから自分の操縦の仕方を学ぶ」と同じ用途を、claude-recall なら MCP の `recall_search` と `recall_export` で担えると考えられる

## 問い

- このリポの Claude Code セッションは worktree を多用する。worktree が消えたセッションを `c` で MCP 経由に回す経路は、EnterWorktree 後の再開でどこまで使えるか（実際に入れて試す）
- @b4b4r07 の「介入パターン→ハーネス修正案」は、どの粒度の介入（訂正・中断・やり直し）を信号にしているのか。pstack の `/correct` と同じ発想をセッション書庫の上で自動化できるか
- FTS5 のみの決定論的検索で、記憶の drift を避ける代わりに取りこぼす問いは何か（語彙が一致しない過去の決定を引けるか）
- 「元の JSONL の7%ほど」は自分の `~/.claude/projects` でも再現するか（`recall import` 後に `vault.db` と JSONL 合計の大きさを比べる）
- 空白のない日本語は部分一致に落ちるが、「認証 エラー」のように空白を含む日本語クエリは FTS5 側に回って取りこぼすのか
- この vault の CLAUDE.md に「過去の判断は recall で探す」の一言を足すか（作者が挙げる「確実に使わせる手」。ingest やレビューの判断経緯は log.jsonl でなく会話にしか残っていない）

## 関連

- [[concepts/skills-over-memory]] — push/pull の切り分け。claude-recall は pull 側の実装
- [[tools/claude-code-obsidian-sync]] — 同じ会話履歴を Obsidian の Markdown へ落とす別実装
- [[concepts/correction-routing]] — 人間の訂正を共有知へ書き戻すループ。@b4b4r07 のハーネス自己改善の用途と対応
- [[concepts/computer-history-daily-note]] — 客観ログを自動で集め、人が振り返りを足すデイリーノート
- [[concepts/session-history-mirror]] — 溜めたセッション履歴を自己分析に使うプロンプト
- [[concepts/agent-memory-layer]] — 共有メモリ層の設計。claude-recall が「記憶システムではない」と線を引く相手側
- [[tools/pstack]] — 同名の `/recall` スキルを持つ別ツール
- [[tools/claude-code-mods]] — プラグインの Claude Code 内表示が使う hooks module（Claude Mods）
- [[concepts/ai-session-handover]] — 引き継ぎノートを書き出す push 型の引き継ぎ。`recap` と `c` は生の transcript から後で組み立てる pull 型
- [[tools/artifact-share]] — 同じく常駐デーモンを持たず、複数エージェント/プロセスの調停を別の方法で解く coji 製ツール
