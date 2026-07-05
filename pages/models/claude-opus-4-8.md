# Claude Opus 4.8

> **TL;DR**: [[companies/anthropic]] が2026-05-28にリリースした最上位モデルで、長期エージェント実行・正直な自己進捗報告・高精度低再現の挙動と、コストを制御するEffort Control/Fast Modeを特徴とする。

[[models/claude-opus-4-7]] の後継にあたり、価格は据え置きで提供開始された。Anthropicが前世代との差分として挙げるのは「sharper judgment（より鋭い判断力）」「more honesty about its own progress（自己進捗の正直な報告）」「work independently for longer（より長く独立して作業できる能力）」の3点で、TL;DRの各特徴はこの公式の主張に対応する。

**3つの主要な性質。** (1) 長期エージェント実行：多数のツール呼び出しにまたがるタスクで文脈を維持する。高い推論負荷を前提とするため、仕様を少しずつ追加するのではなく最初に全文脈をまとめて渡す設計が有効。(2) セッション途中のsystemプロンプト：messages配列中途の `{"role":"system"}` をプロンプトキャッシュを無効化せずに追加できる。上書き指示としてではなく文脈として扱うと挙動が安定する。(3) ツール利用の再調整：Web検索は以前より頻繁に起動する一方、1回の作業内で繰り返す回数は減り、文脈から回答する傾向が強まった（高精度・低再現率の方向へシフト）。

**コスト制御の仕組み。** Opus 4.8はデフォルトがHigh effortで、Claude CodeとClaude.aiの両方で Effort Control（Low/High/Max）を切り替えられる。Fast Mode は通常の約2.5倍速・約3倍安価で、コードレビュー・定型コード生成・繰り返し処理に向く。[[concepts/claude-code-dynamic-workflows]] は1セッションで最大1,000サブエージェントを並列実行できる。コスト最適化の起点としてまず Effort Control を使うのが効果的（[[concepts/claude-usage-optimization]]）。

**プロンプト調整の実践指針。**

- 思考設定は "x-high" ではなく "high" から始め、複雑度の階層ごとにテストする
- 長期タスクでは全文脈を最初にまとめて渡す（少しずつ追加しない）
- 調査系タスクでは「まず検索する」「サブエージェントに任せる」を明示的に指示する
- レビュープロンプトから保守的な表現を外し、フィルタリングは下流で行う（モデルが指示を文字通りに従うため、上流で絞ると再現率が落ちやすい）

Boris Cherny（Claude Code 作者）は、Opus 4.8 が長時間の自律作業ベンチマークで最優秀という報告が増えていると発信し、自律実行の tips として auto mode で権限確認を省くことと [[concepts/claude-code-dynamic-workflows]] でのオーケストレーション活用を挙げる。長時間自律の評価軸としては、1Bトークン予算でコーディングエージェントの継続動作を測る SWE-Marathon（@rishi_desai2 引用。Slack をゼロからビルド／JAX コードベースを PyTorch へ書き換え／C コンパイラを Rust で実装等のタスクを含む）が登場している。

## 検証済み事実

- 2026-05-30: Anthropic公式が示したOpus 4.8の3機能：Effort Control、Fast Mode（約2.5倍速度）、[[concepts/claude-code-dynamic-workflows]]（最大1,000並列エージェント）
- 2026-05-29（CodeRabbitベンチマーク）: 評価対象は軽微・小規模・大規模の複雑度階層からサンプリングした100件のオープンソースPR
- 2026-05-29（CodeRabbitベンチマーク）: フルシステムのパス率はOpus 4.8 72% vs ベースライン 68%（+4ポイント）
- 2026-05-29（CodeRabbitベンチマーク）: 実用的な指摘のパス率はOpus 4.8 61% vs ベースライン 62%（誤差範囲内）
- 2026-05-29（CodeRabbitベンチマーク）: 致命的な指摘（critical）が35件→29件に減少
- 2026-05-29（CodeRabbitベンチマーク）: コストは1回あたり$0.20〜0.28（Opus 4.5は約$0.13、Sonnet 4.5は$0.04〜0.12）
- 2026-05-29（CodeRabbitベンチマーク）: major指摘が119件→81件に減少し、minor/nitpickがほぼ倍増（コメント全体のノイズはベースラインより増加）

## 観察ログ（未検証）

- 2026-05-29: レビュー用途で200kトークンを超えるとコンテキスト性能が目に見えて低下し、モノレポや大規模コードベースは壁にぶつかる可能性（単一ソースの観察・要検証）
- 2026-05-30 @zodchiii: Effort Control が Opus 4.8 で最もコスト削減効果の高い新機能で、プロンプトの60%をLow・10%のみMaxに振ると月額コストを半減できるとの主張（単一ソースの数字）
- 2026-05-29: Opus 4.8 の正直性——自己コードの欠陥を未報告のまま放置する確率が旧世代比4倍低下、欠陥ある結果を無批判に報告する率は0%（単一ソースのスコア）

## 問い

- 200kトークン超での性能低下は自分のモノレポ規模で実際にボトルネックになるか、Fast Modeで緩和できるか
- 「プロンプトの60%をLowに振ってコスト半減」は自分のワークロードで再現するか、品質劣化とのトレードオフはどの程度か
- レビュー用途で保守的プロンプトを外して下流フィルタする運用は、CodeRabbitのノイズ増加問題を回避できるか

## 関連

- [[companies/anthropic]]
- [[models/claude-opus-4-7]]
- [[tools/claude-code]]
- [[tools/autoreview-skill]]
- [[concepts/claude-code-dynamic-workflows]]
- [[concepts/claude-usage-optimization]]
