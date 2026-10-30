# ChatGPT Pro/Business の月次 API クレジット

> **TL;DR**: ChatGPT の Pro・Business 契約に、OpenAI API で使える月次クレジットが付くようになった（Pro $100／$200／$500、Business は席ごとに $20／$100 を上限 $500 でプール）。対象は自作アプリ・エージェントの API 利用で、Codex は API キー経由でも対象外。Anthropic が3週間前に始めた Max/Team 向け施策と額・条件がほぼ一致する。

@OpenAIDevs が 2026-10-28（UTC）に「今日から、対象の ChatGPT Pro・Business 契約者は毎月最大 $500 の API クレジットを受け取ってアプリやエージェントを作れる」と発表した。Anthropic が 2026-10-07 に始めた [[business/claude-plan-api-credits]] と同じ構造で、サブスクリプション（ChatGPT・Codex の利用枠）と API 従量課金という別々の財布のうち、API 側に毎月サブスク代とほぼ同額を無料で積む。この発表を引用した @gunta85（Günther | グンタ。GProductionsJP の CEO・Design Engineer）は両社の付与額を円建てで並べ、「どっちもサブスク代と同額が毎月付く。最初に一回紐付けないともらえない」「Claude Code 勢は 10/7 からもらえてたけど、気づいてない人多そう」と書いている。条件の正本は OpenAI のヘルプセンター記事と開発者ドキュメントで、以下はそこからの転記である。

## 付与額

| プラン | 月次クレジット |
| --- | --- |
| Pro（$100 ティア） | $100 |
| Pro（$200 ティア） | $200 |
| Pro（$500 ティア） | $500 |
| Business Standard 席 | 1席 $20（ワークスペースでプール） |
| Business Premium 席 | 1席 $100（ワークスペースでプール） |
| Business ワークスペース上限 | 月 $500（年払いは年 $6,000） |

Business は $20 × Standard 席数 ＋ $100 × Premium 席数 と $500 の小さい方になる。ヘルプ記事の例では Standard 5席＋Premium 3席で $400、Standard 10席＋Premium 4席は上限前の $600 が $500 に切られる。初回の付与は課金期間の残り日数で日割りされ、年払いの Business は1年分をまとめて受け取る。Plus・無料トライアル・Enterprise・Edu・旧 Codex 席は対象外。

@gunta85 は日本の価格で次のように読み替えている（円換算は本人のもの）。

| プラン（@gunta85 の表記） | 月額 | 毎月の付与 |
| --- | --- | --- |
| Pro | 1.8万円 | 1.6万円分 |
| Pro | 3.5万円 | 3.2万円分 |
| Pro | 8.8万円（年96万） | 8万円分 |
| Business | Standard 席 / Premium 席 | 3,200円 / 1.6万円分（全体で上限8万） |
| Plus | 3,500円 | 0円 |

## 受け取り方と条件

- 対象プランで有料契約が7日以上続いていること。契約が対象でも付与は自動では始まらず、紐づけ（activation）を一度行う
- ChatGPT のアカウントと API 側のアカウントで同じメールアドレスを使う
- Pro は契約者本人が受け取り先 API 組織の Owner でもあること。Business はワークスペースの Owner かつ API 組織の Owner で、メンバーや Admin だけでは紐づけできない。API 組織側では billing を管理できる権限が要る
- 紐づけは ChatGPT の Settings → Billing（Pro）またはワークスペースの billing 設定・Admin Console（Business）から「API credits benefit」を開き、受け取り先の API 組織を確認して確定する。API Platform の Billing 画面から始める経路もある
- API を使ったことがなければ、同じメールアドレスの Personal API Organization に紐づけられる。API 側のオンボーディング完了は不要
- 1つの契約・ワークスペースは1つの API 組織にだけ紐づけられ、API 組織側も1本のリンクしか持てない。変更はサポート経由
- 一度紐づければ、以後は契約更新のたびに自動で付与される。毎月の再申請は不要

@gunta85 は「罠」として、加入7日後から・繰越なし・紐付け先は1つで変更はサポート経由、という点が Claude 側と共通で、ChatGPT 側だけに「ChatGPT と API で同じメアド必須」が加わると整理している。

## 使える範囲・使えない範囲

| 対象 | 対象外 |
| --- | --- |
| Responses API・Chat Completions | Codex（API キー経由も対象外） |
| Agents API（OpenAI ホストのサンドボックス料金を含む） | ChatGPT・サインイン済み Codex の利用上限（増えない） |
| Decisions API | 個人の usage-credit 残高・Business ワークスペースのクレジット残高（加算されない） |
| GPT-Live・Realtime API | ChatGPT Business のプラグイン・クレジット・プロモーション（別の施策） |
| Images API | — |
| 組み込みツール（Web 検索・ファイル検索・Code Interpreter）とその利用・ストレージ料金 | — |
| Playground | — |

Business のメンバーは ChatGPT の席を持っていても API 組織・プロジェクトへのアクセスは別途付与が要り、API の利用量や上限が ChatGPT Business 側に移ることもない。

## 残高の挙動

- 課金サイクル末で失効し、繰り越しなし。年払い Business は年サイクル末に失効
- 無料クレジットが購入クレジットより先に消費される。使い切ったとき、購入分があればそちらで継続し、無ければ API リクエストは HTTP 429・エラーコード `credit_balance_exhausted` で失敗する。続けるには購入か Auto Recharge
- API 側に支払い方法を登録しなくても無料クレジットで使える。usage tier は条件を満たせば自動で上がる。クレジット残高があってもレート制限・モデル制限・プロジェクト上限は外れない
- 解約すると以後の付与は止まり、付与済み分はサイクル末まで使える。Plus・Go・Free へのダウングレードはサイクル末にリンクが外れる。Pro のアップグレードや Business の席追加・席種アップグレードは上限内で日割りの追加付与、席の付け替えは追加付与なし
- Pro から Business に移るとリンクは切れ、Business の有料契約が7日続いた後に Owner が新しく紐づける。Business から Enterprise に移るとサイクル末にリンクが外れる
- 紐づけを行った Owner が Business ワークスペースを離れるとリンクが外れ、そのサイクル中は再リンクできない
- 全額返金が認められると、その請求に紐づく未使用クレジットは消える
- ヘルプ記事は提供が「有効化されたコホートと国」に依存し、施策自体が変更・終了しうると書いている

## Claude 側との対照

@gunta85 が「Pro/Max は差額が消費税だけ」「Team/Business は席の値段も付与額も上限も完全一致」と並べた対応関係を、両社の公式記事の値で書き直すと次のようになる。Claude 側の値は [[business/claude-plan-api-credits]] に転記した Anthropic サポート記事による。

| | ChatGPT（OpenAI・2026-10-28 開始） | Claude（Anthropic・2026-10-07 開始） |
| --- | --- | --- |
| 個人上位プラン | Pro $100 / $200 / $500 | Max 5x $100 / Max 20x $200 |
| チームプラン | Business Standard 席 $20・Premium 席 $100・上限 $500 | Team Standard 席 $20・Premium 席 $100・上限 $500 |
| 入門プラン | Plus は対象外 | Pro は対象外 |
| 自社コーディングエージェント | Codex は API キー経由でも対象外 | 対話型の [[tools/claude-code]] は対象外。紐づけた Console の API キーで自分で動かす `claude -p`・Agent SDK は対象 |
| 待機期間・繰越・紐づけ先 | 7日・なし・1組織（変更はサポート経由） | 7日・なし・1組織（変更はサポート経由） |
| 支払い方法の登録 | API 側に不要 | Console 側に不要 |
| 追加条件 | ChatGPT と API で同じメールアドレス | — |

2社の条件がここまで揃うのは、先に出した Anthropic の施策に OpenAI が3週間で追随したためと考えられる（wiki 側の推論。両社の公式記事は互いに言及しない）。はっきり違うのは自社コーディングエージェントの扱いで、Anthropic は API キーで動かすヘッドレスの Claude Code と Agent SDK をクレジットの対象に入れ、OpenAI は Codex を経路を問わず外している。@gunta85 は使い道として「自作エージェント、CI ボット、夜間バッチ、自分のアプリ」を挙げている。

## 問い

- ChatGPT Pro の $100 ティアを契約している場合、Codex が対象外なので使い道は Agents API・Responses API での自作バッチに限られる。[[tools/openai-codex]] の「2本目のエンジン」戦略に月 $100 の API 枠を足すなら、Codex 本体でなく API 側のどの工程（レビューの下請け・夜間バッチ）に回すと見合うか
- 2社が3週間差で同額・同条件を出した。Google・xAI など他社が追随するか、あるいは Codex・Claude Code 本体がクレジットの対象に入る方向に条件が緩むか
- ヘルプ記事は提供が「コホートと国」で異なると書く。日本の Pro 契約で実際に Billing 画面に表示されるか。@gunta85 は「今日から」と書くが、本人が受け取ったという記述はソースに無い

## 関連

- [[business/claude-plan-api-credits]] — 3週間先行した Anthropic 側の同型施策。付与額・上限・7日・繰越なしまで一致する
- [[tools/openai-codex]] — クレジットは Codex に使えない（API キー経由も対象外）。サインイン済み Codex の利用上限も増えない
- [[companies/openai]] — 提供元
