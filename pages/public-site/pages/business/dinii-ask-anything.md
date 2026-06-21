# Dinii ask-anything（社内問い合わせ代行Bot）

> **TL;DR**: 「いまのデータ・ログ・コードを人が読みに行かないと答えが出ない」社内問い合わせを、[[tools/claude-managed-agents]]ベースのSlack botに代行させ、開発チームへの正式チケットを約半減させた運用事例。

RAGベースのbot（過去事例の意味検索）がカバーできるのは「過去に誰かが答えた問い」だけで、**いまのシステムをread-onlyで調査して初めて答えが出る問い**は人手の代行が長く残る。ask-anythingはこの層を狙い、BigQuery/Cloud Logging/monorepo grep等のツールを複数回叩いて調査をやり切る点が、検索型botとの決定的な違い。

## 拾うタスクの3類型（すべてread-only調査）

| 類型 | 例 | 従来の所要 | 導入後 |
|------|----|-----------|--------|
| **データ調査** | 「◯◯店の昨日の客数」「直近7日のリピート率」「ある店舗のshopId」 | エンジニアがBigQueryでテーブル定義確認→SQL作成→実行。1件10〜15分 | botがSQL組立→dry-runでコスト確認→本実行→Slack整形。1件1〜2分 |
| **ログ・履歴調査** | 「レジ開閉記録をCSVで」「△△の操作ができない、何が起きた？」「決済が時々失敗、原因は？」 | CS/店舗運用→エンジニアへ依頼→Cloud Logging filter or BigQuery抽出→CSV化。1件15〜30分＋待ち時間 | botがCloud Logging/BigQueryを自分で叩き時系列で読み、必要ならCSVをSlackにupload。1件1〜3分 |
| **仕様・バグ確認** | 「この機能の今の仕様は？」「◯◯すると△△が起きる、バグ？仕様？」 | PdM/CS→エンジニアがIDEでgrep→依存を辿り仕様と実装を照合。数十分〜数時間（手が空くまでの待ち込み） | botがmonorepoをread-onlyで配置し内部からrg grep→関連ファイル・該当処理の要約＋「仕様通り/バグの可能性」の判断材料を返す |

## なぜ自前オーケストレーションを捨てたか

前世代のRAG botは**Mastra**（TypeScriptのAI orchestrationフレームワーク）でAnthropic SDKを直叩きし、「LLM出力→ツール呼び出し→結果を投げ返す」ループを自前で約100行書いていた。ask-anythingは必要なツール種別が桁違い（BigQuery / Cloud Logging / monorepo / Notion / GitHub / Sentry / Slack / channel-talk）で、これら全てに自前でアダプタ・credential rotate・retry/ストリーム切断ハンドリングを書くと、本来prompt設計に充てたい時間が定型的な「つなぎ」コードに吸われる。

Claude Managed Agentsへ載せ替えた結果、自前コードは3種類に収束：
1. Slackイベントを受ける薄いHTTP handler
2. Managed Agentsのsession作成・events中継のrelay
3. agentから呼ばれるcustom tool本体（BigQuery実行・Cloud Logging実行・CSV upload等）

「ダイニーならではのbusiness logic」は3だけで、1・2はほぼdispatcher。**前世代のorchestrationコードの体感7割が不要になった**。Managed Agentsの3つの効きどころ＝(a) Anthropic側sandboxでagentが自分でコード実行・ファイル保存、(b) MCP server URL登録だけで外部SaaSのcredential管理をAnthropic側に寄せられる、(c) Skillをagent本体と別ファイルで管理しpublish即本番反映。

## KPI（2026年4月末本格稼働〜4週）

| 指標 | 推移 |
|------|------|
| **bot参加スレッド数/週** | 4/27週 24 → 5/4週 46 → 5/11週 96 → 5/18週 109（4週で約4.5倍） |
| **dev-helpチケット起票数/週** | 導入前 約45件（36〜49）→ 5/4週 20 → 5/11週 35 → 5/18週 24（**ほぼ半減**） |
| **エスカレーション率**（bot回答スレッドが結局チケット化された割合） | 4/27週 100% → 5/4週 約45% → 5/11週 約35% → 5/18週 22.0% |

質問の絶対数が減ったのではなく（Slackの質問総数はむしろ増加）、正式チケット化される前にbotが答えを返す量が増えた、という意味。5/18週には「bot回答スレッドの約8割は追加チケット化不要」な状態に到達。

## 運用上の罠（別記事予告分）

- GCP credentialをsandboxに渡せない問題
- MCP server URLのtrailing slash一つでcredentialが見つからなくなる罠

## 問い

- この3類型（データ/ログ/仕様）は自分のwiki Queryでも同型か。「過去事例検索」と「いまのシステム調査」の切り分けはPKMにも効くか
- 「自前orchestrationの7割が不要」はManaged Agents一般に成り立つか、ツール数が多い事例特有か。[[tools/slack-bolt]]＋自前ループとのコスト比較は
- エスカレーション率100%→22%を駆動したのは「skill整備」と「custom tool精度向上」のどちらが支配的か

## 関連

- [[tools/claude-managed-agents]] — 本事例の実装基盤（sandbox・MCP・Skill）。read-only調査botの実運用ケース
- [[tools/slack-bolt]] — Slack App構築フレームワーク（イベント受信層）
- [[business/backoffice-ai-implementation]] — AI=一次担当/人間=判断モデルの社内実装事例（グッドパッチ）
- [[concepts/multi-agent-patterns]] — 複数ツールを叩き調査をやり切るエージェント設計
- [[companies/anthropic]] — Claude Managed Agents提供元
