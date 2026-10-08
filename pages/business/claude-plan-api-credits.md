# Max/Team プランの月次 API クレジット

> **TL;DR**: Claude の Max・Team プランに、Claude Platform（API）で使える月次クレジットが付くようになった（Max 5x $100／Max 20x $200／Team は席数に応じて最大 $500 をプール）。対象は自作アプリ・エージェントの API 利用で、対話型の Claude Code やプランの使用量上限は対象外。

これまでサブスクリプション（claude.ai・Claude Code の使用量）と API 従量課金は別の財布だったが、@ClaudeDevs が2026-10-07に発表したこの施策で、サブスク契約者が API 側でも毎月一定額を無料で試せるようになる。@ClaudeDevs は「Haiku 5.5 を含むどのモデルでも、自分のコードやサードパーティのハーネスで使える」と述べた（[[models/claude-haiku-5-5]]）。条件の詳細は Anthropic のサポート記事が正本で、以下はそこからの転記である。

## 付与額

| プラン | 月次クレジット |
| --- | --- |
| Max 5x | $100 |
| Max 20x | $200 |
| Team（Standard 席） | 1席 $20 |
| Team（Premium 席） | 1席 $100 |
| 割引 Team（Nonprofit・Scientists） | Standard 席 $20・Premium 席 $100 |

Team は全席分を1つの月次残高にプールし、上限は $500。サポート記事の例では Standard 3席＋Premium 2席で月 $260 になる。Free・Pro・Enterprise は対象外。

## 受け取り方と条件

- 有効な Max/Team 契約で、対象プランに7日以上いること。Console 側の支払い方法登録は不要
- claude.ai の Billing 設定から Claude Console の組織を1つ「Link organization」で紐づける（Max は契約者本人、Team は Owner／Primary Owner。Console 側は Owner・Admin・Billing ロール）
- 紐づけられる組織は1つだけで、自分では変更できない（変更はサポート経由）

## 使える範囲・使えない範囲

| 対象 | 対象外 |
| --- | --- |
| Claude API（Messages API・Message Batches API） | 対話型 Claude Code（ターミナル・IDE・デスクトップ・Web） |
| Console の Playground | Claude・Claude Code・Claude Cowork の追加利用（extra usage） |
| Claude Managed Agents | Amazon Bedrock・Google Cloud Vertex AI・Microsoft Foundry 経由の Claude |
| Claude Agent SDK | — |

`claude -p` と Agent SDK は、紐づけた Console 組織の API キーで自分で動かす場合に限りクレジットの対象になる（Agent SDK 利用として課金されるため）と、サポート記事は説明している。Claude プランでサインインした状態の `claude -p` はプランの使用量上限から引かれ、Claude Code GitHub Action・IDE 拡張・デスクトップアプリから起動した実行は `-p` 付きでも Claude Code 利用扱いでクレジット対象外になる。

## 残高の挙動

- 請求サイクルごとに付与され、繰り越しなし（未使用分はサイクル末で失効）。年契約でも毎月付与
- 購入済みクレジットより先に消費される。紐づけ組織の API キー保持者全員が同じ残高を使うため、プロジェクトや人ごとに絞るには Console のワークスペース支出上限を使う
- 使い切ったとき、購入クレジットや auto-reload があればそちらで継続し、無ければ次の付与まで API リクエストが止まる。Claude プラン側に課金されることはない
- 解約・対象外プランへのダウングレードで新規付与は止まり、付与済み分は失効まで使える。Max 5x→20x のアップグレードは日割りで即時付与

## 問い

- このリポの launchd headless ingest（`claude -p`）をプランのサインインから Console 組織の API キーに切り替えると、月 $100〜$200 の範囲に収まるか。プラン使用量の圧迫が減る分と、切り替えの手間は見合うか
- 付与額で [[models/claude-haiku-5-5]] を下請けに回す構成なら、どの程度の量を毎月無料で回せるか

## 関連

- [[models/claude-haiku-5-5]] — @ClaudeDevs がクレジットの使い道として名指ししたモデル
- [[tools/claude-code]] — 対話型利用はクレジット対象外。`claude -p` は認証方法で扱いが分かれる
- [[concepts/cost-effective-harness]] — 安価モデルへの委譲の損益分岐。クレジットで試算の前提が変わる
- [[companies/anthropic]] — 提供元
