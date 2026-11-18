# 自分の働き方を -mode スキルに蒸留する（pstack `/automate-me` と Grok Bot 向け移植）

> **TL;DR**: Lauren Tan（@poteto）の pstack にある `/automate-me` は、直近 2〜4 週間のエージェントとの会話記録を 3 分割して掘り、2 つ以上の分割に現れた習慣だけを `<名前>-mode` スキルに書いて、エージェントが利用者の流儀で働くようにする手順。AI コンテンツ制作者の Carnage（@0xCarnagee）はこれを Grok Bot 用の 1 本のプロンプトに移植し、採掘→質問→分類→下書き→削る→各 Bot に「このモードに従え」の 1 行を入れる、の 6 段で公開した。

解く問題は、Bot が既定のチャットボットの流儀で返すために、利用者が「短く」「言い直して」「もっと任せて」を会話のたびに繰り返すことである。pstack の入口スキル `poteto-mode` は Lauren 自身の流儀（返答の長さ・委任の仕方・検証の姿勢・散文の規律）を書いたもので、`/automate-me` は同じ形のスキルを利用者ごとに作る（[[tools/pstack]]）。Carnage は投稿で Lauren を「SpaceXAI のエンジニア」と紹介し「自分自身をプロンプトに複製した」と書くが、この言い方は Carnage のもので、Lauren 自身の X プロフィールは「grok @bot at @SpaceXSI」と記す（2026-10-31 時点のキャッシュ）。原典の SKILL.md は cursor/plugins リポジトリで公開されており、本ページはその本文と Carnage の移植版を並べて置く。

## 原典 `/automate-me` の流れ（SKILL.md の転記）

SKILL.md は自らを「利用者の作業上の慣習を、エージェントが従うスキルに変える案内つきの流れ」と説明し、出力は利用者向けの `-mode` スキル 1 つ（例: `jay-mode`・`priya-mode`）とする。採掘の工程・Cursor 組み込みの `create-skill`（執筆）・`unslop` スキル（散文の規律）の 3 つを順に呼ぶ編成役で、それらの代わりにはならない、と書く。`disable-model-invocation: true` なので利用者が明示的に呼んだときだけ動く。

0. **既存スキルの確認** — `.cursor/skills/**/*-mode/SKILL.md` と `~/.cursor/skills/*-mode/SKILL.md` を利用者のハンドル名で再帰的に探す。あれば `AskQuestion` で「既存を更新（繰り返し実行の既定）／最初から（まれ・理由を聞く）」を確認する。更新モードでは、最後に編集した日時（`git log -1 --format=%cI`）以降の履歴だけを掘り、「ゼロから何を捉えるか」でなく「何が変わったか・欠けているか」を聞き、利用者が否定していない節は保ち、新しい証拠のある節だけ直す
1. **履歴を掘る** — システムプロンプトが示すワークスペースの `agent-transcripts/` だけを読む。`~/.cursor/projects/*/` を横断して glob すると無関係なプロジェクトの私的な会話を読んでしまうので禁じる。直近 2〜4 週間を 3 つに分割し、分割ごとにサブエージェントを並列に走らせ、証拠の場所つきの短い構造化リストを返させる。探す信号は、返答の好み（長さ・口調・形式・「もっと簡単に」の訂正）・委任の癖（サブエージェント・モデル・専用ワークフロー・並列度）・検証の姿勢（「完了」の意味・単体テストか実機再現か・レビュアー）・コードと散文の規律（原則・lint と整形ツール）・工程の慣習（worktree・コミット・PR・レビューとマージの道具）・メタの好み（タスク中にスキルを直す・新しいスキルを提案する）。**2 つ以上の分割で見えたパターンは高確信、単独の信号は弱く通常は落とす**
2. **利用者に直接聞く** — 採掘ではまだ話題に出ていない意図は拾えない。利用者に自由入力させず `AskQuestion`（選択式）を使い、4〜6 択の質問を 1〜2 問（カテゴリの質問は複数選択可）、「どの領域が大事か」から入って選ばれた領域を具体的に掘り、最後に自由記述を 1 問。「20 問を投げない」
3. **分類する** — 返答の流儀・自律性（どこまで聞かずにやるか・MCP の使用）・まず理解する（調査時にどのスキルを使うか）・サブエージェント・散文とコードの規律・レビューと検証・工程・スキルの 8 節のうち当てはまるものだけ使う。粒度の手本として `poteto-mode` を読むが内容は写さない。「利用者のルールは poteto-mode のルールと同じではない」
4. **下書きする** — `create-skill` で執筆する。置き場は `.cursor/skills/<handle>-mode/SKILL.md`（個人用なら `~/.cursor/skills/`）。frontmatter の `description` は利用者の名前と `/<handle>-mode` と「この人の流儀で働く」で発火させ、「コードを書く」のような一般語では発火させない。`disable-model-invocation: true` を既定にする
5. **散文を磨く** — `unslop` と `create-skill` の執筆指針を全行に当て、下書きを見せて意見をもらい、何度でも削る。「モードスキルはマニュアルではない」
6. **着地させる** — main から切った worktree で作業し、コミットして PR を開く。main へ直接 push しない

ガードレールは 6 条である。1 つの会話に過適合しない（一度言われて別の時に覆った好みはノイズ）・賢ぶらない（他スキルの言い換え・比喩・詩的な散文はコストだけ）・貼らずに参照する（利用者が頼るスキルや原則文書はパスで指す）・節は最小に（「明確に伝える」は節にならず、「短い段落。比較は表。箇条書きは本当に並列のときだけ」なら節になる）・名前は一般形に（命令文は著者の名前でなく「the user」）・対称性を強いない（工程のルールが無ければ工程の節を省く）。評価については「モードスキルは主観的な出力なので create-skill 型のテストと反復のベンチマークは役に立たない」とし、利用者との感触確認（自分らしく読めるか・見落としは無いか）で出荷する。使わない場面は、タスク固有のスキルが欲しいとき（`create-skill` だけでよい）と、「コミットメッセージの書き方」のような 1 つの作業手順を捉えたいとき（通常のスキルであってモードスキルではない）。

## Carnage の Grok Bot 向けプロンプト（最初のリプライの転記）

Carnage は投稿本文で 5 段（Mine→Filter→Ask→Write→Install）に要約し、「プロンプトは最初のリプライに」として次を載せた。原典にある `AskQuestion`・`create-skill`・`unslop`・worktree は Grok Bot に無いので、ファイルパスと Bot の指示文に置き換えている。

```
<role>
You are my clone engine. Turn how I work into ONE skill every Bot follows. The method is /automate-me from pstack by Lauren Tan (cursor/plugins on GitHub), adapted for Grok Bot.
</role>

<setup>
My name for the file: [YOUR_NAME]. The file: /workspace/modes/[YOUR_NAME]-mode.md
If it already exists, update it in place and keep every rule I haven't contradicted.
</setup>

<steps>
01 MINE
Read what you can see of my last 2 to 4 weeks: our chats, your memory, and anything I put in /workspace/me/ (exported chats, reviews, docs I wrote). Split it into 3 slices and hunt for:
style: length, tone, format, every time I said "dumb it down"
delegation: when I fan out, to which Bots, what runs in parallel
verify: what "done" means to me
discipline: the rules I cite, the words I always cut
process: how I ship, review and hand off
meta: when I fix the instructions instead of the task
Keep a pattern only if it shows up in 2 or more slices. Drop one-offs.

02 ASK
Ask me ONE multiple choice question (4 to 6 options) about which areas matter most, then one follow up on what I picked, then one open question for anything the options missed. Never more.

03 CLUSTER
Group what you kept into sections: style, autonomy, understand first, delegation, discipline, review and verify, process, skills. A section exists only if I have a non-default rule there.

04 DRAFT
Write the file. Operational, not poetic. Say "the user" in imperatives, not my name. Point to the skills and files I rely on, never paste them.

05 CUT
Cut every line that doesn't change what an agent does. A mode skill is not a manual. Show me the draft, take my edits, cut again.

06 INSTALL
Ask me first, then add one line to each Bot's instructions: "follow /workspace/modes/[YOUR_NAME]-mode.md". If you can't edit a Bot, give me the line to paste. When I say "update my mode", mine only what changed since the file was last edited.
</steps>

<output>
mode: the file path
sections: only the real ones
evidence: where each rule was seen
dropped: the one-offs you ignored
Then ask me: does it read like me? did it miss anything?
</output>
```

原典と移植版の対応（両ソースからの転記）:

| 項目 | 原典 `/automate-me`（Cursor） | Carnage 版（Grok Bot） |
|---|---|---|
| 入力 | ワークスペースの `agent-transcripts/` を 3 分割し並列サブエージェントで読む | 会話・Bot のメモリ・`/workspace/me/`（書き出した会話・レビュー・自分が書いた文書）を 3 分割 |
| 採用基準 | 2 つ以上の分割に出たパターン。単独は落とす | 同じ（2 つ以上の分割。単発は落とす） |
| 質問 | `AskQuestion` で 4〜6 択を 1〜2 問＋自由記述 1 問 | 4〜6 択を 1 問＋選んだ項目の追問 1 問＋自由記述 1 問 |
| 節 | 8 節のうち非既定のルールがあるものだけ | 同じ 8 節 |
| 出力先 | `.cursor/skills/<handle>-mode/SKILL.md`・`disable-model-invocation: true` | `/workspace/modes/<名前>-mode.md` |
| 散文 | `unslop` と `create-skill` の指針で削る | 「操作的に、詩的でなく」「エージェントの動作を変えない行を全部削る」 |
| 着地 | worktree で PR | 各 Bot の指示に「follow /workspace/modes/<名前>-mode.md」の 1 行（編集できなければ貼る行を渡す） |
| 更新 | 最終編集以降の履歴だけ掘る | 「update my mode」で最終編集以降の変化だけ掘る |
| 評価 | 自分らしく読めるか・見落としは無いか | 同じ 2 問に加え、各ルールの根拠の場所と落とした単発の習慣を出力させる |

## 位置づけ（wiki 側の読み）

- [[tools/pstack]] の README は `/automate-me` を「実際の働き方から下書きした自分用の `-mode` スキルが欲しいとき」の 1 行で載せる。本ページはそのスキル本体で、pstack 全体が「作者の流儀を他人が借りる」道具であるのに対し、このスキルだけが「借りた流儀を自分のものに差し替える」向きを持つ
- Lauren が利用者向けに書いた [[concepts/grok-bot-team-guide]] の「指示のたびに Bot にスキルを作らせる」は作業手順のスキル化で、`/automate-me` はその上に置く利用者自身の流儀のスキル化にあたる。SKILL.md が「1 つの作業手順はモードスキルでなく通常のスキル」と線を引くのはこの区別である
- 「2 つ以上の分割に出た習慣だけ」という閾値は、[[concepts/coding-agents-as-management]] で Osmani が書く「同じことを 2 回以上直したら CLAUDE.md かスキルへ」と同じ型の規則で、どちらも単発の訂正を規則化しない閾値を 2 に置く。モードスキルは、CLAUDE.md がリポジトリについて書くことを人について書いたものと読める
- [[concepts/skills-over-memory]] の「教訓はメモリでなくスキルへ」と同じ側に立つ。Carnage 版は会話記録と Bot のメモリを原料にしてモードファイルを作るので、メモリ（蓄積）からスキル（規則）への蒸留をこのプロンプトが担う
- モードスキルは [[concepts/agent-trust-inverse-pyramid]] の散文層にあたる。ガードレールの「節は最小に」「動作を変えない行は削る」は、同じ Lauren が散文層に時間を割かないとする配分と整合する

## 問い

- この vault の自己モデル（mind-os と self-recall スキル）は、人手のインタビューで作ったモードスキルにあたる。`/automate-me` の「会話記録で 2 分割以上に出た習慣だけ」を併用すると、インタビューでは言語化されない習慣（毎回の「短く」の訂正）を拾えるか
- Carnage 版は Bot ごとに 1 行を足す。Bot が増えたあとモードを更新したとき、ファイル参照なら自動で伝わり、貼り付けた Bot には伝わらない。どちらを既定にするか
- 原典は「モードスキルは主観的な出力なのでベンチマーク型の評価は役に立たない」と書く。[[concepts/eval-loop]] のような品質ゲートを個人の流儀に当てる方法はあるか、それとも感触確認で足りるか

## 関連

- [[tools/pstack]] — 原典のスキル群。`poteto-mode` が作者の流儀、`/automate-me` が利用者の流儀を作る
- [[concepts/grok-bot-team-guide]] — 同じ Lauren の利用者向け 7 項目。「指示のたびにスキルを作らせる」の上に置く利用者自身のモード
- [[tools/grok-bot]] — Carnage 版が対象にする製品。`/workspace/` 配下のファイルと Bot の指示文で動く
- [[concepts/coding-agents-as-management]] — Osmani の「2 回直したら CLAUDE.md へ」。規則化の閾値を 2 に置く同型の規則
- [[concepts/skills-over-memory]] — メモリでなくスキルへ教訓を置く方針。モードスキルはその個人版
- [[concepts/agent-trust-inverse-pyramid]] — 同じ Lauren の 4 層配分。モードスキルは散文層
- [[concepts/organic-architecture]] — 同じ Lauren の講演。散文層を薄く保つ配分の出所
