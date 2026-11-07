# Claude のヘルパー・セッション管理・Side Chat／Split Screen・Docs・/skill-doctor・Mods：AI Edge の「Claude 2.0」ガイド

> **TL;DR**: 複数のヘルパー（サブエージェント）に仕事を分けて本線が回収する、1 つの管理セッションから他のセッションを監督する、Side Chat と Split Screen で本線を止めずに確認する、Docs／Sheets をその場で編集する、/skill-doctor で文脈を食うスキルを見つける、Mods で挙動を自作する、という Claude Code（デスクトップ）の直近数週間の変更 7 項目を「試すプロンプト」つきで並べた AI Edge（@aiedge_）の X 記事。プロンプト本文は本 vault のキャッシュに収録されていない。

「Claude 2.0」は記事の見出しで、本文中にその名で呼ばれる公式発表への言及はない。AI Edge は X のプロフィールで「AI Edge by @milesdeutscher」と名乗るメディアアカウント（フォロワー約 8 万・ニュースレター運営）で、記事は「Claude はこの数週間で多くの人が気づいている以上に変わった」と始め、7 節で機能と使い方を並べる。各節に「Prompt:」として貼り付け用のプロンプトを添えているが、キャッシュでは本文が落ちており、以下は機能の説明部分の転記である。記事の軸は「複数のエージェントを見渡し、進捗を確認し、複数のプロジェクトを見失わずに管理する」ことにあり、7 節のうち前半 3 節がそれに当たる。

## I. ヘルパー（サブエージェント）のチームを付ける

AI Edge は、Claude に「ヘルパー」のサブエージェントのチームを付けられるようになったと書く。ヘルパーはサイドチャットとして立ち上がり、調査・執筆・計画を別々のチャットでやる代わりに、本線の Claude に主タスクを渡して各ヘルパーに並列で部分を担当させ、終わったら本線が成果を回収する。例として 49 ドルのデジタル製品を出すとき、競合調査・X の投稿・販売ページの 3 つを順に頼むのでなく一度に渡す使い方を挙げる（そのプロンプト本文は未収録）。各ヘルパーの進捗は、機能が有効なところでは task panel から追える。大きな継続プロジェクトでは、Claude Code に承認済みのソースファイルのリポジトリを持たせると、ヘルパーごとに事実の版が食い違うのを防げる、と AI Edge は述べる。

wiki 側の対応: ヘルパーは [[tools/claude-code-subagents]] のサブエージェント、本線が回収する構造は [[concepts/claude-code-orchestration]] の subagent 運用に当たる。

## II. 他のセッションを 1 つのチャットから管理する

Claude Code は対応するデスクトップのセッション間で働けるようになり、1 つのセッションが他のセッションの状況を調べ、更新を求め、変更を伝えられる、と AI Edge は書く。複数の記事・ビルド・調査を並行しているなら「管理セッション」を 1 つ作るプロンプト（未収録）を勧め、他所に効く修正を管理セッションから渡す例も挙げる。AI Edge はこれを「複数のタスクを同時に監督するより実用的な方法」と位置づける。

## III. Side Chat と Split Screen

**Side Chat** は Claude Code の左上のタブメニューから開くか、チャットに `/btw` と打つと開く。Side Chat は本線の文脈を既に持っているので、本線のタスクを止めずに質問・前提の確認・明確化ができる、と AI Edge は書く（[[tools/claude-code]] のコマンド表にある `/btw` の UI 側の入口）。

**Split Screen** は複数の Claude Code セッションを並べて開く機能で、サイドバーの別チャットを右クリックして「Open in → Split view」を選ぶか、Mac なら Option、Windows なら Alt を押しながらチャットをクリックする。例として、競合調査・コンテンツ執筆・サイト構築の 3 セッションを同時に見る使い方を挙げ、対応するパネルは別ウィンドウに切り離してモニター間で動かせるとする。

## IV. Docs と Google Sheets を Claude の中で直接編集する

AI Edge は「Claude Docs は新しい」とし、下書きをレビューして一緒に直し、仕上がった文書を共有・エクスポートできる、Claude Design と Slides も同じワークフローに近づいた、と書く。スプレッドシートについては、調査の残りを文脈に保ったまま実際のシートを更新できるとし、例のプロンプトは未収録。Google 側のサイドバーから Docs・Sheets・Slides を編集する機能は [[tools/claude-google-workspace]] に別途ある。

## V. 文脈を食っているスキルを掃除する

Skills とプラグインを足すほど Claude がセッションに持ち込む指示が増え、ほとんど使わないものが文脈を占めうる。Claude Code の新コマンド `/skill-doctor` で、どのスキルが最も文脈を消費しているか・どれを使っていないかを見て、続けて不要な指示を聞く（プロンプト未収録）と、不要に場所を取っている指示が示される、と AI Edge は書く。指示ファイルとスキルの肥大を同じ問題として扱う [[concepts/skills-over-memory]] の、Claude Code 側の計測入口に当たる。

## VI. Mods で Claude をカスタマイズする

Claude Code は Mods に対応し、公式の設定を待たずにアプリの動き方の一部を変えられる。例として、長いタスクが終わったら結果の 1 行要約つきでデスクトップ通知を出す Mod を挙げる（作らせるプロンプトは未収録）。Mods は TypeScript で書かれプラグインに同梱でき、Claude Code 自体と同じコンピュータへのアクセスを持つので信頼できるものだけ動かせ、と AI Edge は注意する。仕組みの正本は [[tools/claude-code-mods]]。

## VII. 小さな更新が入った既存機能

- **Dispatch**: 既にアクセスのあるアカウントでは、スマホから仕事を割り当ててデスクトップで結果を受け取れる。ローカルファイルを使うタスクでは、Claude Desktop を起動したままコンピュータを起こしておく必要がある
- **Claude Design**: ワンページャー・プロトタイプなどのビジュアル作業を同じ会話で作って磨ける。デザインシステムを渡せば既存ブランドに沿う
- **Plugins と Skills**: 全部自作する前にライブラリを見よ。執筆・マーケ・調査・財務のワークフローは既にあるかもしれない

記事は末尾で AI Edge のニュースレター（newsletter.aiedgehq.co）への登録を促す。

## 問い

- `/skill-doctor` を本 vault で走らせ、使っていないスキルがどれだけ文脈を食っているか測る。本リポはスキルとプラグインが多い
- 管理セッションから他セッションへ「修正を伝える」機構は、[[concepts/claude-code-orchestration]] の agent teams と同じものか、デスクトップ固有の別経路か。記事は UI 操作しか書いていない
- 元記事（x.com/i/article/2117212475154169856）からプロンプト本文を取り直す価値があるのは I（ヘルパー）と II（管理セッション）の 2 本か

## 関連

- [[tools/claude-code]] — 本体。`/btw` と `/doctor` はコマンド表にあり、本ページは Side Chat・Split Screen・`/skill-doctor` の UI 側の入口を足す
- [[concepts/claude-feature-map]] — 同じ「機能の所在と試すプロンプト」形式で Claude 全体の 20 機能を並べた @angeldot_ のガイド。本ページは Claude Code デスクトップの直近の変更に絞った版
- [[concepts/claude-code-orchestration]] — 「自分が指揮官になり専門化した Claude を並べる」運用フレーム。本ページのヘルパー・管理セッション・Split Screen はその UI 側
- [[tools/claude-code-subagents]] — ヘルパーの実体であるサブエージェントの定義と挙動
- [[tools/claude-code-mods]] — VI の Mods の仕組み（Function Hooks・5 層・プラグイン同梱）
- [[tools/claude-google-workspace]] — IV と対になる、Google 側サイドバーからの Docs／Sheets 編集
- [[concepts/skills-over-memory]] — V が測るスキル・指示ファイルの肥大問題
- [[tools/claude-code-remote-control]] — VII の Dispatch と同じ「スマホから自分の Claude Code を動かす」運用の既存ページ
- [[tools/claude-code-plugins]] — VII で「先にライブラリを見よ」と言われる公式プラグイン群
- [[concepts/workflow-cloning]] — AI Edge の運営者 Miles Deutscher 本人のアカウントから取り込んだ別ページ
