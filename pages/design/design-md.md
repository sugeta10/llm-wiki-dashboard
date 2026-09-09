# DESIGN.md

有名サービスのデザイン仕様を `.md` 形式でまとめたコンテキストファイル。プロジェクトに置くだけでコーディングエージェントが自動で参照し、バイブコーディングで起きがちな「デザインがダサい」問題を緩和する。

CLAUDE.md / AGENTS.md がエージェントの**行動ルール**を与えるのに対し、DESIGN.md は**デザインの参照仕様**（既存サービスの配色・タイポ・レイアウト規範）をプロジェクト文脈に常駐させる発想。エージェントは趣味（taste）を言葉で説明されるより参照を与えられた方が良い出力を出す、という [[design/claude-premium-website-build]] と同じ前提に立つ。frontend-design スキルがバックグラウンドで「やってはいけない凡庸パターン」を禁止するのに対し、DESIGN.md は「真似るべき良い基準」をプロジェクト側に置く点が補完的。

@miz_apps_ は「バイブコーディングでデザインがダサいのにお悩みの方」に向けてこのやり方を勧め、プロジェクトに置くだけでエージェントが「いい感じに参照してくれる」と体験ベースで述べている（X bookmark 1,757・2026-06-06 時点）。

仕様自体は Google が策定した **DESIGN.md spec** に基づく。既製のDESIGN.mdを探すなら **getdesign.md**（`getdesign.md`）が、有名ブランド80弱（Apple・Stripe・Linear・Tesla・Vercel・Claude・Figma・Nike・Spotify 等）のデザインシステム分析を「パターン・トークン・ルール」単位で配布するカタログサイトになっている。各ブランドの配色・タイポ・レイアウトの個性（例: Teslaは「徹底した引き算・全画面写真・ほぼゼロUI」、Linearは「超ミニマル・紫アクセント」）を一文要約＋詳細ページで提供する。

## 実例: 公的デザインシステムをスライド生成スキルに使う

参照仕様の供給元は有名サービスに限らない。@itnavi2022 は2026-09-08、**デジタル庁デザインシステムの Markdown ファイル**を使って PowerPoint 作成スキルを作ったと投稿し、ダウンロード用の Google Drive リンクを添えている。ChatGPT に設定して使う想定で告知されている。

参照先が民間サービスのブランドでなく公的機関が公開しているデザインシステムであること、出力が Web UI でなくスライドであることの2点で、DESIGN.md と同じ発想が別の供給元・別の出力先へ広がった例と考えられる。ただし配布ファイルの中身（どの Markdown をどう構造化し、スキル側で何を指示しているか）は Drive リンク先と添付画像にあり未取得のため、スキルの設計はここでは扱わない。

## 観察ログ（未検証）

- 2026-06-06: getdesign.md がGoogleのDESIGN.md specを基盤とするカタログサイトとして稼働。voltagent の awesome-design-md 系プロジェクトが運営し「月間6M+ views」を謳う。BMW M / Binance / Airtable等をfeatured掲載。スポンサー枠あり＝商用ホスティング前提のため、掲載ブランド数・spec準拠の度合いは一次未確認

## 問い

- DESIGN.md は frontend-design / UI/UX Pro Max スキルと併用して衝突しないか。役割分担（禁止ルール vs 参照仕様）は実際に噛み合うか
- 自分のwikiのoutputs/ HTML生成時に、共通の DESIGN.md を置けば視覚品質を底上げできるか
- 有名サービスのデザイン仕様をmd化する作業自体を [[tools/book-to-skill]] 的に自動生成できるか
- 公開されているデザインシステム（デジタル庁など）をそのまま渡す場合と、DESIGN.md spec の形に整形して渡す場合で出力品質は変わるか。整形の手間に見合うか

## 関連

- [[design/claude-premium-website-build]] — 同じ「AIデザインの凡庸さ」問題への制作ワークフロー側の解
- [[concepts/claude-skills]] — frontend-design 等スキルとの補完関係
- [[concepts/llm-doc-management]] — アプリ文脈をドキュメント化してLLMに渡すパターンの一種
- [[concepts/cursor-instruction-methods]] — AGENTS.md/Rules等のプロジェクト常駐コンテキスト手段
- [[design/image2-brand-slides]] — ロゴからデザインシステムを派生させるスライド生成スキル（同系の「AIデザインの凡庸さ」対策）
- [[design/slide-md]] — 同じ「デザインを `.md` で固定」発想のスライド版デザインシステム
- [[concepts/chokkan-karte]] — 参照画像の世界観をAIに言語化させてから発注に使うEC/クリエイティブ応用。tasteを言葉でなく素材で渡す同じ前提の逆方向（素材→言葉）
- [[tools/emil-kowalski-skills]] — 「エージェントにはtasteがない」問題をスキル（誤りリスト＋直し方）で解く同系アプローチ。/apple-designはWWDCデザイン原則の.md化
- [[design/ui-pattern-dictionary]] — 同じ「デザインパターンを体系化して参照可能にする」発想を、AI向けコンテキストファイルでなく人間向けの名称辞典として実装したもの
- [[design/ochiai-ai-design-method]] — 同じ「AIデザインが凡庸になる」問題への別解。既存仕様の参照でなく形容詞をオブジェクトへ分解する指示設計側からのアプローチ（落合陽一の方法論を@kimuai08が再構成）
- [[tools/diagram-design]] — 同じ問題の「図」版。デザイン仕様ファイルでなく対象サイトを読んで色・フォントを寄せる
- [[design/consulting-pptx-skill]] — スライド生成側でデザイン規約をファイルに溜めてAIに毎回読ませる実装。@itnavi2022 のデジタル庁デザインシステム版と同じく「参照仕様をPPTX生成に効かせる」系統
- [[design/lusion]] — 価値のほぼ全量がモーションと3D演出に置かれた制作事例。テキスト抽出では何も残らず、「.md化して渡す」アプローチの適用限界を示す
