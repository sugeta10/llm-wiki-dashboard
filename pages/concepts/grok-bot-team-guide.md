# Grok Bot の Bot 設計と運用ガイド（Lauren・Grok Bot チーム）

> **TL;DR**: Grok Bot 開発チームの Lauren（@poteto）が「質の高い Bot を作りチームを管理する方法」として示した短いガイド。Bot 設計役の dr eggbot を入れる・Bot は少数精鋭・指示を出すたびに Bot 自身にスキルを作らせ private な GitHub リポジトリに保存する・コネクタと vault と仕事の道具を全部渡し Bot 専用のメールで分離する・手作業とコピペの往復を Bot に肩代わりさせる・インターンを迎えるつもりで自己紹介から始める・音声で脳内ダンプして「自分の言葉で言い直して」で理解を確認する、の 7 項目。

> 📌 X bookmark: 15,844（2026-11-01 時点）

Lauren は X のプロフィールで SpaceXSI の Grok Bot 担当・React Compiler コアチーム・前職 Cursor／Meta／Netflix と名乗り、この wiki では Cursor 向けスキル群 [[tools/pstack]] の作者、エージェントを信頼するための 4 層配分 [[concepts/agent-trust-inverse-pyramid]]、1 文のゴール復唱プロンプト [[concepts/goal-restatement-prompt]] の発信者として既に登場している。本ガイドは製品を作る側の人物が、利用者に「Bot をどう作りどう管理するか」を 7 項目で示したもので、投稿の冒頭で「@bot にこの投稿を読ませて、あなたのチームの改善を手伝わせろ」と、Grok Bot が X の投稿を読んで自分の構成に反映できることも前提にしている。[[tools/grok-bot]] の @lingxi の運用記が開発チームの大規模運用、[[concepts/grok-bot-one-person-harness]] や [[concepts/grok-bot-ai-team-workspace]] が利用者の個別構成であるのに対し、本ページは製品側が勧める最小の作法にあたる。

## 7 項目（Lauren の説明を転記）

1. **dr eggbot をチームに入れる**。Lauren が自分の Bot を作るときに使う「Bot 設計役」の Bot で、投稿末尾にその共有ページを置いている（次節）
2. **Bot はたくさん要らない**。少数の良い Bot のほうが、大所帯より役に立ち、管理も楽になる
3. **指示を出すたびに Bot 自身にスキルを作らせる**。タスクのやり方を教えるたびにスキル化させると、長い会話のなかでも一貫してこなせる。スキルを独立して残したいなら、Bot に private な GitHub リポジトリを作らせて全スキルをそこに保存させてもよい
4. **コネクタを追加し、vault を使い、自分の仕事で使う道具すべてへのアクセスを渡す**。Bot に自分専用のメールアドレスを用意させ、Bot 自身の名義でアカウント登録させると分離がよくなる
5. **手でやっている作業と、Bot に情報をコピペで戻している工程を洗い出す**。その作業を代わりにどうやれるか Bot に尋ねる
6. **インターンを雇ったつもりで迎える**。正しく効率よく仕事をしてもらうためにどうオンボーディングするかを考え、まず自己紹介をして自分の仕事を説明し、どこを自動化できそうか Bot に尋ねる
7. **いちばんのお気に入り：音声モードか文字起こしを使う**。大量の情報を一気に吐き出すのに向く。最後に「これを自分の言葉で言い直して」と頼めば、Bot が理解したかを確かめられる

## dr eggbot（Bot を設計する Bot）

投稿末尾のリンク先（x.ai の共有ページ）によれば、dr eggbot は「質の高い Grok Bot を設計する」Bot で、好みを尋ねる質問を少しした後に CreateAgent で Bot を作る。コーディング用の Bot には poteto-mode の基準（仕事は 1 つ・スロップなし・検証済み）を課し、コーディング以外の Bot にも同じ締まり（仕事は 1 つ・声は 1 つ・やらないことを明示・余分なツールを残さない）を求める。口調はカジュアルで少しマッドサイエンティスト風、短い小文字。仕事がはっきりしたら動く側に倒し、共有テンプレートを既定にはしない、と説明されている。共有ページには「この AI Bot はサードパーティの利用者が作ったもので SpaceXSI 製ではなく、あなたの代わりに行動しうる」という定型の免責が付く。Bot の指示本文はページに含まれない。

[[tools/grok-bot-essential-bots]] で kaito が「真っ先に入れるべき Bot」としたのも同じ dr eggbot で、そちらは `x.ai/bot/marketplace/bots/dr-eggbot-v2` のマーケットプレイス形式、本投稿は `x.ai/bot/_jOdbfkB16zxu7MRcmReE` の個別 ID 形式でリンクしている。両者が同一の Bot か版違いかは未確認。

## 位置づけ（wiki 側の読み）

- **少数精鋭**は、[[tools/grok-bot]] で @lingxi が「単一領域に集中させたときに最も良く働く」と述べたことと、dr eggbot の「仕事は 1 つ」の基準の延長にある。利用者側の構成では [[concepts/grok-bot-one-person-harness]] が Operator 1 体から始める順序を取り、[[concepts/grok-bot-ai-team-workspace]] は最初から担当別 7 体を置く。製品側の助言は前者に近いと読める
- **指示のたびにスキルを作らせる**は、@lingxi の「学んだことをリポジトリのスキルに固めさせる」と同じで、[[concepts/skill-self-improving-loop]] が人手のレビューを挟んで回す工程を Bot 自身に委ねる形である。private な GitHub リポジトリへの保存は、スキルを Grok Bot の外に持ち出せる状態にしておく助言で、[[concepts/grok-bot-opus-5-5-harness]] が「ハーネスは製品、脳は交換可能」と読んだ構図のなかで、利用者が自分の資産として持てるのはスキルとルーチンだという含意があると考えられる
- **Bot 専用のメールと Bot 名義のアカウント**は、人間の資格情報を Bot に渡さないという点で [[tools/1password-for-agents]] と目的を共有するが、向きが逆である。1Password for Agents は人間のアカウントへのサインインを承認付きで代行し、Lauren の助言は Bot に別人格のアカウントを持たせて人間の資産から切り離す。[[concepts/grok-bot-ai-team-workspace]] の「ログインは本人だけ」という RULES とも対照になる
- **「自分の言葉で言い直して」**は、同じ Lauren の [[concepts/goal-restatement-prompt]]（AI にこちらのゴールと問題を復唱させる 1 文）を、音声で大量に話した後の理解確認に使う具体例で、[[tools/pstack]] の `/in-your-own-words` スキルと同じ内容である
- **インターンとして迎える**は @lingxi の「有能なインターンとして扱う」と同じ比喩で、オンボーディング（自己紹介・仕事の説明）から入る点が、ルールや憲章を先に書く [[concepts/grok-bot-one-person-harness]] の会社ブリーフと順序が違う。Lauren は「どこを自動化できるか」を Bot 自身に尋ねさせており、自動化の設計も Bot に委ねる姿勢が一貫している

## 問い

- 「少数の良い Bot」の数の目安は示されていない。dr eggbot で作った Bot が増えたとき、[[tools/grok-bot-essential-bots]] の trimmy のような整理役が要るのはどの時点か
- Bot が自分で作るスキルは誰がレビューするか。[[concepts/agent-trust-inverse-pyramid]] でいう散文層にあたり、増え続ければ古いルールを外す手順（[[concepts/instruction-patch-lifecycle]]）が要る
- Bot 名義のメールとアカウントで分離する方式と、1Password for Agents で人間のアカウントを承認付きで使わせる方式のどちらを既定にするか。サービス側の利用規約が Bot 名義の登録を許すかも確認が要る

## 関連

- [[tools/grok-bot]] — 基盤製品。@lingxi の開発チーム運用記に対し、本ページは製品チームの Lauren が利用者向けに書いた最小の作法
- [[tools/grok-bot-essential-bots]] — kaito 選の Bot 10 体。本ページの dr eggbot と、Bot の指示を整理する trimmy を含む
- [[concepts/grok-bot-one-person-harness]] — @beamnxw の Operator 1 体から始める 7 手順。本ページの「少数精鋭」と順序が近く、ブリーフ先行かオンボーディング先行かが違う
- [[concepts/grok-bot-ai-team-workspace]] — 炎鎮の担当別 7 体構成。「たくさん要らない」の対照例で、「ログインは本人だけ」の RULES は Bot 名義アカウントの助言と逆向き
- [[concepts/grok-bot-opus-5-5-harness]] — ハーネスと脳の分離の読み。スキルを GitHub に保存する助言は利用者側の資産を外に出す形
- [[concepts/goal-restatement-prompt]] — 同著者の「自分の言葉で言い直させる」1 文。本ページの 7 項目目はその音声入力後の使い方
- [[concepts/agent-trust-inverse-pyramid]] — 同著者の 4 層配分。Bot が自分で作るスキルは散文層にあたる
- [[tools/pstack]] — 同著者の Cursor スキル群。dr eggbot がコーディング Bot に課す poteto-mode の基準の出どころ
- [[concepts/skill-self-improving-loop]] — 会話からスキルを育てる 3 段ループ。本ページは同じ工程を Bot 自身に委ねる
- [[tools/1password-for-agents]] — 人間の資格情報を Bot に見せずサインインさせる承認レイヤー。Bot 名義アカウントで分離する助言と目的は同じで向きが逆
