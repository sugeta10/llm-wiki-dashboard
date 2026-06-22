# AutoHedge

> 孤立理由: 自律型ヘッジファンドという金融×マルチエージェントの具体OSSで、既存ページに該当が薄い（[[concepts/multi-agent-patterns]] の Specialist Team 実装例として関連付け）

Pythonで構築された**自律型ヘッジファンド**のOSS。役割分担した4つのAIエージェントで運用パイプラインを構成する点が特徴で、[[concepts/multi-agent-patterns]] の Specialist Team（役割特化エージェントの分業）を金融領域に適用した実装例にあたる。

4エージェントの役割分担:

| エージェント | 役割 |
|------------|------|
| Director | 投資テーゼ（投資仮説）を生成 |
| Quant | テーゼを検証 |
| Risk Manager | ポジションサイズを決定 |
| Execution | 注文を発注 |

ライブ（実運用）で動作するとされる。

## 観察ログ（未検証）

- 2026-05-31: @itsharmanjot の「無料なのが信じられない優良GitHubリポジトリ10選」スレッドの1件目として紹介。ライブ運用を謳うが、実際の運用成績・バックテスト結果は提示されていない。

## 問い

- 「ライブで動作する」の実態（実弾運用かペーパートレードか）と、4エージェント分業が単一エージェントより意思決定品質を上げているかは未検証。
- [[tools/claude-finance-agents]]（Anthropic公式の金融10エージェント）と役割設計を比較できるか。

## 関連

関連: [[concepts/multi-agent-patterns]] · [[tools/claude-finance-agents]]
