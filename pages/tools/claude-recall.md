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

## 他ページとのつながり（wiki 側の推論）

- [[concepts/skills-over-memory]]（@mvanhorn）が言う「push と pull を分け、recall を残したいなら pull 側へ移す」を、claude-recall はツールの設計方針として明文化している。README の比較表の「注入: Push / Pull」の行がそのまま対応する
- [[tools/claude-code-obsidian-sync]]（ペパボ）は同じ JSONL を Obsidian の Markdown に落とす。claude-recall は Markdown 化せず SQLite に生のまま溜めて検索と再開に使う点が異なり、@b4b4r07 の日次ダッシュボードは [[concepts/computer-history-daily-note]] の「機械が事実を集め、人が振り返る」型を Claude Code の作業に限って実装したものと読める
- 「人間の介入パターンからハーネスの修正案を自動で作る」は、[[concepts/correction-routing]] の「訂正を共有知へ書き戻す」ループや [[concepts/improver-skill-pattern]] の observer skill を、セッション書庫を入力にして回す形と考えられる。投稿は用途を述べるのみで、介入の検出方法や修正案の精度は示されていない
- 名前が同じ [[tools/pstack]] の `/recall` スキルは、チャット履歴と共有記録からトピックの現状ブリーフを再構築する別物である。@poteto の [[concepts/agent-trust-inverse-pyramid]] の手順2「`/recall` で過去のトランスクリプトから自分の操縦の仕方を学ぶ」と同じ用途を、claude-recall なら MCP の `recall_search` と `recall_export` で担えると考えられる

## 問い

- このリポの Claude Code セッションは worktree を多用する。worktree が消えたセッションを `c` で MCP 経由に回す経路は、EnterWorktree 後の再開でどこまで使えるか（実際に入れて試す）
- @b4b4r07 の「介入パターン→ハーネス修正案」は、どの粒度の介入（訂正・中断・やり直し）を信号にしているのか。pstack の `/correct` と同じ発想をセッション書庫の上で自動化できるか
- FTS5 のみの決定論的検索で、記憶の drift を避ける代わりに取りこぼす問いは何か（語彙が一致しない過去の決定を引けるか）

## 関連

- [[concepts/skills-over-memory]] — push/pull の切り分け。claude-recall は pull 側の実装
- [[tools/claude-code-obsidian-sync]] — 同じ会話履歴を Obsidian の Markdown へ落とす別実装
- [[concepts/correction-routing]] — 人間の訂正を共有知へ書き戻すループ。@b4b4r07 のハーネス自己改善の用途と対応
- [[concepts/computer-history-daily-note]] — 客観ログを自動で集め、人が振り返りを足すデイリーノート
- [[concepts/session-history-mirror]] — 溜めたセッション履歴を自己分析に使うプロンプト
- [[concepts/agent-memory-layer]] — 共有メモリ層の設計。claude-recall が「記憶システムではない」と線を引く相手側
- [[tools/pstack]] — 同名の `/recall` スキルを持つ別ツール
- [[tools/claude-code-mods]] — プラグインの Claude Code 内表示が使う hooks module（Claude Mods）
