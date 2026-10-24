# Grok Bot「絶対入れたほうが良い」10選（kaito 選）

> **TL;DR**: Grok Bot の利用者 kaito（@cu30rry_）が「個人的にこれは絶対入れたほうが良い」と厳選した Bot 10 体の一覧。Bot を設計する dr eggbot と他の Bot の指示を整理する trimmy を筆頭に、タスク追跡・競合監視・解説動画・ブックマーク整理・AI ニュースとセキュリティの監視・開発管理・赤入れ確認を揃える。kaito は定期的に更新すると宣言している。

[[tools/grok-bot]] は自前の計算機を持つ常駐エージェントで、1 体ごとに指示とルーティンを持ち、他の利用者が作った Bot を取り込める。kaito は X のプロフィールで、日本のソフトウェアエンジニア兼テックリードであり Cursor・GrokBot・pstack・Claude Code でソフトウェアを作ると名乗り、[[tools/pstack]] の Claude Code 対応も告知した人物。この一覧は Bot の作者名と導入リンクまで添えた実用向けのカタログで、kaito は 1 件目の dr eggbot だけを「Grok Bot で真っ先にいれるべき Bot」と位置づけている。

## 10 体の一覧（kaito の説明を転記）

| # | Bot | 作者 | できること（kaito の説明） |
|---|---|---|---|
| 1 | [dr eggbot](https://x.ai/bot/marketplace/bots/dr-eggbot-v2) | @poteto | 質問に答えるだけで、やることを 1 つにしぼった質の高い Grok Bot を設計して作る。会話履歴から Bot を改善し、ルーティンのコスト改善も行う。「真っ先にいれるべき Bot」 |
| 2 | [Commitments](https://x.ai/bot/rFShmowW_3x_qeXQQB-sn) | @samlambert | Slack・Notion・メールから自分のやるべきことを見つけて Notion に記録し、終わるまで追跡する。タスクの登録忘れと「タスクを登録するためのタスク」が日常から消える |
| 3 | [trimmy](https://x.ai/bot/DLSYM3q3eUsy4Si4fTPwa) | @TimDOES | ほかの Bot の指示やルーティンを読み、古いルールの削除や重なっている部分のまとめなど改善案を番号付きで出す。やってほしい番号を伝えるだけで直す |
| 4 | [Stalk Bot](https://x.ai/bot/marketplace/bots/stalk-bot) | （記載なし） | 自分専用の調査用メールで競合のニュースレターや製品に登録し、使い始めの流れを動画に残す。サイト・料金・求人・X の変化を見張り、自分たちとの違いと次の一手を報告する |
| 5 | [Explainer Video bot](https://x.ai/bot/rFn9Q2pLJKFkSF4JN3Spx) | @ChaosLensX | 確かめた数字・AI ナレーション・動くグラフィック・実際の映像を使い、そのまま投稿できる解説動画を作る |
| 6 | [Bookmark Debt Collector](https://x.ai/bot/kh0FE5498wURU-QxPfLfW) | @harris_p10 | X のブックマークから「誰のどんな投稿を保存しているか」を 1 枚の画像にまとめる。週 1 回、「読む・行動する・消す」の提案付きで届けてもらうこともできる |
| 7 | [AI Radar](https://x.ai/bot/i1xwguSTrplEdPvx96MYh) | @gota_bara | AI 企業の公式アカウント・研究者・ブログ・GitHub・論文などを見張り、大事な AI ニュースだけを 1 行と元のリンクで届ける |
| 8 | [Sentinel](https://x.ai/bot/pYlSkzY_JDuMEZDuseCMO) | @badmorgan420 | X を見張り、新しい脆弱性・実際に悪用されている攻撃・大きな情報漏えいなどのセキュリティニュースを知らせる |
| 9 | [Engineering Lead](https://x.ai/bot/marketplace/bots/engineering-lead) | @poteto | 開発の作業を小分けにしてクラウドのエージェントに任せ、テスト・レビュー・止まっている作業を追いかける。困ったとき・マージできるとき・終わったときだけ知らせる |
| 10 | [akapen bot](https://x.ai/bot/Afsn2TQt9PTy8v1LFhtXb) | @AI_masaou | 作る前に案を確認用のページで見せ、こちらが赤ペンで直してから作業を進める。デザインの方向性のすり合わせや長い文章の添削に便利 |

導入リンクは投稿の短縮 URL を展開したもので、`x.ai/bot/marketplace/bots/<名前>` 形式（dr eggbot・Stalk Bot・Engineering Lead）と `x.ai/bot/<ID>` 形式（残り 7 件）の 2 種類がある。両者の違い（マーケットプレイス掲載か個別の共有リンクか）は投稿に説明がなく、未確認。

## 一覧の読み方（wiki 側の推論）

kaito は分類を示していないが、役割で分けると 5 つに読める。Bot 群そのものを作り整えるもの（dr eggbot・trimmy）、自分の仕事を追わせるもの（Commitments・Engineering Lead）、外の変化を見張るもの（Stalk Bot・AI Radar・Sentinel）、成果物を作る前後で人の確認を挟むもの（Explainer Video bot・akapen bot）、溜まったものを片付けるもの（Bookmark Debt Collector）。先頭 2 体が「Bot を作る Bot」と「Bot を掃除する Bot」である点は、Bot の数が増えるほど指示の整理が仕事になるという運用上の順序を反映していると考えられる。

## 他ページとのつながり（wiki 側の推論）

- trimmy の「古いルールの削除・重なりのまとめ」は、[[concepts/instruction-patch-lifecycle]] が人手の手順として書く「指示は期限付きパッチで、効いていない行を外す」作業を Bot に肩代わりさせたものと読める
- dr eggbot の「やることを 1 つにしぼった Bot を設計する」は、[[tools/grok-bot]] で @lingxi が述べる「単一領域に集中させたときに最も良く働く」と同じ設計方針
- Engineering Lead は、[[tools/grok-bot]] の @lingxi 運用（クラウドエージェントの起動・催促・PR までの追跡と、例外だけ人間に戻す）を 1 体の Bot として配布した形と考えられる。作者の @poteto は [[tools/pstack]] の作者でもある
- Bookmark Debt Collector は、この vault の X ブックマーク取り込みと同じ入力を扱う。あちらは 1 枚の画像と「読む・行動する・消す」の提案、こちらは wiki ページ化で、ブックマークを「ためこんだまま」にしない目的は共通
- AI Radar と Sentinel は、[[concepts/ai-news-source-automation]] の「情報源を深夜に巡回し要点だけ届ける」運用を Grok Bot 上で行う形。監視対象が公式アカウント・研究者・論文（AI Radar）と脆弱性・漏えい（Sentinel）に特化している
- Commitments は [[concepts/ai-secretary-gateway]] の「依頼を受け付けて今日決めることだけ戻す窓口」に近く、入力を Slack・Notion・メールに固定して Notion で追跡する実装
- akapen bot の「作る前に案を確認ページで見せ、赤ペンで直してから進める」は、[[tools/html-plan]] が実装プランを HTML 1 枚にして選択・コメントを貼り戻させる流れと同型

## 問い

- dr eggbot で Bot を作り trimmy で整理する組み合わせは、[[concepts/instruction-patch-lifecycle]] の「失敗のたびに足し、世代が変わったら外す」運用を人手なしで回せるか。外した結果が悪化したときの戻し方は誰が持つか
- Bookmark Debt Collector の「読む・行動する・消す」の 3 分類は、この vault の「全部 wiki 化する」方針と比べて何を残し何を捨てるか。「消す」の判断基準を自動化できるか
- Grok Bot の提供地域・料金は [[tools/grok-bot]] の観察ログでも未確認のまま。kaito が日本から使っている事実は、少なくとも日本で利用できることを示す

## 関連

- [[tools/grok-bot]] — Bot の基盤。@lingxi のエンジニアリング運用記に対し、本ページは利用者が選んだ Bot のカタログ
- [[tools/pstack]] — 同じ @cu30rry_ が Claude Code 対応を告知したスキル群。作者 @poteto は本一覧の dr eggbot と Engineering Lead の作者
- [[concepts/ai-news-source-automation]] — 情報源を巡回して要点だけ届ける運用。AI Radar・Sentinel はその Grok Bot 版
- [[concepts/instruction-patch-lifecycle]] — 指示を期限付きパッチとして外す手順。trimmy が自動化する対象
- [[concepts/ai-secretary-gateway]] — 依頼の窓口に限定した AI 秘書。Commitments が近い役割
- [[tools/html-plan]] — 作る前に案を 1 ページで見せ指摘を貼り戻す Claude Code プラグイン。akapen bot と同型
