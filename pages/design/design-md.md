# DESIGN.md

有名サービスのデザイン仕様を `.md` 形式でまとめたコンテキストファイル。プロジェクトに置くだけでコーディングエージェントが自動で参照し、バイブコーディングで起きがちな「デザインがダサい」問題を緩和する。

CLAUDE.md / AGENTS.md がエージェントの**行動ルール**を与えるのに対し、DESIGN.md は**デザインの参照仕様**（既存サービスの配色・タイポ・レイアウト規範）をプロジェクト文脈に常駐させる発想。エージェントは趣味（taste）を言葉で説明されるより参照を与えられた方が良い出力を出す、という [[design/claude-premium-website-build]] と同じ前提に立つ。frontend-design スキルがバックグラウンドで「やってはいけない凡庸パターン」を禁止するのに対し、DESIGN.md は「真似るべき良い基準」をプロジェクト側に置く点が補完的。

仕様自体は Google が策定した **DESIGN.md spec** に基づく。既製のDESIGN.mdを探すなら **getdesign.md**（`getdesign.md`）が、有名ブランド80弱（Apple・Stripe・Linear・Tesla・Vercel・Claude・Figma・Nike・Spotify 等）のデザインシステム分析を「パターン・トークン・ルール」単位で配布するカタログサイトになっている。各ブランドの配色・タイポ・レイアウトの個性（例: Teslaは「徹底した引き算・全画面写真・ほぼゼロUI」、Linearは「超ミニマル・紫アクセント」）を一文要約＋詳細ページで提供する。

## 観察ログ（未検証）

- 2026-06-04: @miz_apps_ が「バイブコーディングでデザインがダサいのにお悩みの方」向けに推奨。プロジェクトに置くだけでエージェントが「いい感じに参照してくれる」との体験ベースの推薦（X bookmark 1,757 / 2026-06-06時点）
- 2026-06-06: getdesign.md がGoogleのDESIGN.md specを基盤とするカタログサイトとして稼働。voltagent の awesome-design-md 系プロジェクトが運営し「月間6M+ views」を謳う。BMW M / Binance / Airtable等をfeatured掲載。スポンサー枠あり＝商用ホスティング前提のため、掲載ブランド数・spec準拠の度合いは一次未確認

## 問い

- DESIGN.md は frontend-design / UI/UX Pro Max スキルと併用して衝突しないか。役割分担（禁止ルール vs 参照仕様）は実際に噛み合うか
- 自分のwikiのoutputs/ HTML生成時に、共通の DESIGN.md を置けば視覚品質を底上げできるか
- 有名サービスのデザイン仕様をmd化する作業自体を [[tools/book-to-skill]] 的に自動生成できるか

## 関連

- [[design/claude-premium-website-build]] — 同じ「AIデザインの凡庸さ」問題への制作ワークフロー側の解
- [[concepts/claude-skills]] — frontend-design 等スキルとの補完関係
- [[concepts/llm-doc-management]] — アプリ文脈をドキュメント化してLLMに渡すパターンの一種
- [[concepts/cursor-instruction-methods]] — AGENTS.md/Rules等のプロジェクト常駐コンテキスト手段
- [[design/image2-brand-slides]] — ロゴからデザインシステムを派生させるスライド生成スキル（同系の「AIデザインの凡庸さ」対策）
- [[design/slide-md]] — 同じ「デザインを `.md` で固定」発想のスライド版デザインシステム
- [[concepts/chokkan-karte]] — 参照画像の世界観をAIに言語化させてから発注に使うEC/クリエイティブ応用。tasteを言葉でなく素材で渡す同じ前提の逆方向（素材→言葉）
- [[tools/emil-kowalski-skills]] — 「エージェントにはtasteがない」問題をスキル（誤りリスト＋直し方）で解く同系アプローチ。/apple-designはWWDCデザイン原則の.md化
