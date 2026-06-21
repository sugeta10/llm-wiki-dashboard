# Loop Library

> **TL;DR**: 今すぐ使えるエージェントループの厳選カタログ。ループを探す・自分のループを投稿する・"tokenmaxx"（トークンを惜しまず回す）を掲げる、Forward Future（Matthew Berman）発のリソース。

**Loop Library** は、すでに動くエージェントループ（スケジュールでエージェントを起動し自分に食わせる自動化パターン。[[concepts/loop-engineering]] 参照）を集めた公開カタログ。利用者は登録済みのループを探して流用でき、自分が作ったループを投稿して共有もできる。ループエンジニアリングの「再利用単位」を、個人のリポジトリ内でなくコミュニティ規模で蓄積・配布する層にあたる。

- URL: https://signals.forwardfuture.ai/loop-library/
- 提供: Forward Future（Matthew Berman / @MatthewBerman）
- エージェントへの取り込み: `npx skills add Forward-Future/loop-library --skill loop-library -g`

## 収録ループの構成

カタログ本体を確認した時点で、ループは Engineering・Content・Operations・Evaluation・Design の5カテゴリにまたがり35本前後が公開されている。投稿者は Matthew Berman が最多で、Peter Steinberger・Hiten Shah・Pierson Marks・Jose C. Munoz ら実践者が個別に寄稿している。各ループは「目的1文＋実行プロンプト」の定型で、多くが**停止条件・検証パス・予算上限**を明示する点が共通する（[[concepts/loop-engineering]] の設計原則と整合）。

カテゴリ別の代表例：

| カテゴリ | 代表ループ（投稿者） |
|---|---|
| Engineering | docs sweep＝ドキュメントとコードの同期＋PR起票（Matthew Berman）、architecture satisfaction loop＝小さくテスト済みのチェックポイントで設計をリファクタ（Peter Steinberger）、ticket-to-PR-ready＝最小再現→根因証明→最小修正（Hiten Shah）、Clodex adversarial-review＝CodexにClaudeのPRを反証レビューさせ閾値超の指摘を潰す（Lukas Kucinski）、five-minute repository maintainer＝5分ごとに起きてリポジトリを巡回（Peter Steinberger） |
| Content | SEO/GEO visibility＝検索とAI回答可視性の最大インパクトギャップを順に潰す（Matthew Berman）、product update podcast＝Jellypod MCPで更新を3〜5分ポッドキャスト化（Pierson Marks） |
| Operations | stale-safe batch release＝有効な変更だけ束ねて一括リリース、customer AI deployment＝顧客AI施策を検証→段階展開→監視で1件流す（AgentLed.ai） |
| Evaluation | self-improving champion＝新しいホールドアウトで勝った時だけプロンプト/方針を昇格（Jose C. Munoz）、devil's-advocate＝設計に批判者をぶつけ高インパクトな反論を解消するまで回す、quality streak＝N連続成功まで失敗を回帰テスト化して直す |
| Design | Boeing 747 benchmark＝Three.jsで9アングルをスコアし最弱点を反復改善（@victormustar）、War Loops＝実UIを再現し最弱の視覚/モーション差分を補修（Swayam）、Infinite Clickbait thumbnail＝サムネ案を品質バー到達まで反復（@Alex_FF） |

## 観察ログ（未検証）

- 2026-06-18 @MatthewBerman: 「Loop Library をローンチした。今すぐ使える厳選ループのリスト。ループを見つけ、自分のを投稿し、tokenmaxx」と告知（X bookmark 6,734・impression 757K、2026-06-20時点）。
- 2026-06-21: カタログ本体をingestし収録内容を確認（前回未確認だった件数・構成を補足）。5カテゴリ・35本前後で、各ループはプロンプト全文が公開されている。Matthew Berman 投稿が大半を占めるため「コミュニティ規模の多様性」はまだ限定的で、品質は投稿者依存。実利用前にループ単位で停止条件・検証手段を読む必要がある。

## 問い

- 収録ループは [[concepts/loop-engineering]] の4条件テスト（繰り返し性・自動検証・トークン予算・シニア相当ツール）を満たすものに絞られているか。流用前に自分で4条件チェックすべきか。
- 自分のwiki ingestループ（launchd）をこのカタログ形式の雛形に落とし込めるか。投稿する価値のある汎用ループはどれか。

## 関連

- [[concepts/loop-engineering]] — エージェントループを設計する工学（5段階進化史・6構成要素・MVL・失敗パターン）
- [[tools/claude-code-goal]] — ループの停止条件を担う `/goal`（収録ループの構成要素になりうる）
- [[concepts/multi-agent-patterns]] — 複数ループ/エージェントを編成するパターン
