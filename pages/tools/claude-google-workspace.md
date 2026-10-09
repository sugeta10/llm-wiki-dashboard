# Claude の Google Workspace 連携（Docs・Sheets・Slides）

Anthropic の公式アカウント @claudeai が2026-10-06に、Claude が Google Docs・Sheets・Slides の中で動くようになり、逆にそれらのファイルを Claude 側で開けるようになったと発表した。連携は双方向で、Google のファイル画面に Claude を呼び込む入口と、Claude の画面に Google のファイルを持ち込む入口の2つがある。

> 📌 X bookmark: 20,964（2026-10-09 時点）

## 何ができるか

@claudeai の説明では、Google Workspace 側では Claude がファイル横のサイドバーに常駐し、いま開いているファイルを読み、その場でファイルを直接編集する。編集は1件ずつ、反映される前にユーザーが承認できる。告知に付いた動画は未収集のため、承認画面の見た目・対応プラン・対応地域・Claude 側でファイルを開く操作の具体的な手順はソースからは分からない。

## 位置づけ（wiki 側の推論）

Anthropic は Slack に Claude を常駐させる [[tools/claude-tag]] を先に出しており、今回の連携も「Claude のアプリに作業を持ってくる」のでなく「人が普段使う業務ツールの中に Claude が入っていく」方向の製品と考えられる。Google 自身も Gemini を Gmail・Docs に組み込んで AI 統合プランで売っている（[[companies/google]]）ため、Workspace のサイドバーは Gemini と Claude が同じ画面で競う場所になると推測される。

編集の都度承認を挟む設計は、AI が既定で受信トレイを読む導入が批判された Gmail の事例（[[concepts/gmail-ai-default-access]]）と比べると、AI に書き込ませる範囲をユーザーが1件ずつ決める側に寄せたものと読める。ただし「開いているファイルを読む」範囲が、同じフォルダの他ファイルやリンク先にまで及ぶかはソースに書かれておらず未確認である。

## 問い

- 承認ステップは編集単位でどこまで細かいか（セル1つ・段落1つか、まとめて1回か）。Sheets の数式や Slides のレイアウトを壊さずに編集できるか、実物で1回試す
- Claude 側で Google ファイルを開いたときの読み込み範囲は、既存の Google Drive コネクタと何が違うのか
- Gemini in Docs と並べたとき、同じ文書の推敲・表の集計でどちらに任せるかの判断軸は何か

## 関連

- [[companies/anthropic]] — 提供元
- [[companies/google]] — Workspace の提供元。Gemini を同じ Docs・Gmail に組み込んでいる競合でもある
- [[tools/claude-tag]] — Slack に Claude を常駐させる製品。業務ツールの中に Claude を入れる同じ方向の先行例
- [[concepts/gmail-ai-default-access]] — AI が既定で受信トレイを読む導入への批判。AI にファイルを読ませる・書かせる範囲を誰が決めるかという論点で対になる
