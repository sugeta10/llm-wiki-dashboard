# 「AI っぽくない UI」のための 15 リンク（部品と配色 → エージェント向け設計資産 → 作図と画像処理）

> **TL;DR**: seeco（@seeconvm）が「Fable 5.2 は誰にも AI 製と分からない UI を作れる」として並べた、無料・オープンソースの 15 リンク。shadcn/ui・Tailwind CSS・Magic UI・Lucide・Leonardo の UI 部品と配色、ui-ux-pro-max・frontend-design・theme-factory・DESIGN.md・Astryx・stitch-skills・Figma Context MCP のエージェント向け設計資産、Excalidraw・tldraw・rembg の作図と画像処理。15 のうち 2 項目は同じ anthropics/skills リポジトリ内のスキルで、リポジトリとしては 14 本。

投稿者の seeco はプロフィールに「ai researcher / post about AI / crypto / open for collabs」とある個人アカウントで、デザイナーやツール作者としての肩書は書かれていない。投稿は 487 件ブックマークされた（2026-11-19 時点）。「紫のグラデーションも汎用カードもなし」という枕は、AI 製 UI の凡庸さを指す言い方として [[tools/impeccable]] や [[tools/unslop]] が検出対象に挙げる兆候と同じ語彙で、一覧の狙いもそこにある。末尾の「ブックマークして、この一覧を Fable 5.2 に貼ってランディングページを作り直せ」と、引用先の同じ投稿者の X 記事（Fable 5.2 のモーションデザイン講座）への誘導は宣伝的な枠で、14 本のどのリポジトリの description にも Fable 5.2 への言及はない。同じ「リポ N 選」形式の [[tools/creative-pipeline-repos]]（Florence の映像制作 10 本）と作り方が同じで、見どころも個々のリポの新しさではなく、何を揃えれば「AI っぽくない」に届くかという並べ方にある。

## 15 項目の一覧（投稿の順）

投稿者の一言は投稿からの訳、description 以降は GitHub API の記載（2026-11-19 取得）。

| # | リンク先 | 投稿者の一言 | GitHub の description | owner・license | stars |
|---|---|---|---|---|---|
| 1 | [shadcn-ui/ui](https://github.com/shadcn-ui/ui) | コードとして自分が所有するコンポーネント | 組み合わせ可能でアクセシブルな、配慮された既定値を持つコンポーネント。カスタマイズ・拡張して自分のものにできるコードで、自分のコンポーネントライブラリを作る | 組織（shadcn-ui）・MIT | 128,545 |
| 2 | [tailwindlabs/tailwindcss](https://github.com/tailwindlabs/tailwindcss) | ユーティリティファーストのスタイリング | 素早い UI 開発のためのユーティリティファースト CSS フレームワーク | 組織（tailwindlabs）・MIT | 98,647 |
| 3 | [magicuidesign/magicui](https://github.com/magicuidesign/magicui) | コピペで使えるアニメーション付きコンポーネント | デザインエンジニア向け UI ライブラリ。アプリにコピペできるアニメーション付きコンポーネントと効果。無料・オープンソース | 組織（magicuidesign）・MIT | 23,374 |
| 4 | [lucide-icons/lucide](https://github.com/lucide-icons/lucide) | クリーンで一貫したオープンソースのアイコン | コミュニティ製の美しく一貫したアイコンツールキット。2000 超の SVG ファイル | 組織（lucide-icons）・GitHub API のライセンス欄は NOASSERTION（独自表記） | 25,864 |
| 5 | [adobe/leonardo](https://github.com/adobe/leonardo) | Adobe の、コントラスト比から作る配色 | 望むコントラスト比に基づいて色を生成する | 組織（adobe）・Apache-2.0 | 2,177 |
| 6 | [nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill)（→ [[tools/ui-ux-pro-max]]） | 80 の UI スタイル・配色・フォントペア | 複数プラットフォームでプロの UI/UX を作るためのデザイン知能を提供する AI スキル | 組織（nextlevelbuilder）・MIT | 151,554 |
| 7 | [anthropics/skills — skills/frontend-design](https://github.com/anthropics/skills/tree/main/skills/frontend-design) | AI 製に見えない UI のための Anthropic のスキル | （リポジトリ全体）Agent Skills の公開リポジトリ | 組織（anthropics）・ライセンス欄は未設定 | 197,472（リポジトリ全体） |
| 8 | [anthropics/skills — skills/theme-factory](https://github.com/anthropics/skills/tree/main/skills/theme-factory) | スライド・文書・ランディングページ向けの 10 のテーマ | 同上 | 同上 | 同上 |
| 9 | [google-labs-code/design.md](https://github.com/google-labs-code/design.md)（→ [[design/design-md]]） | 自分のビジュアルアイデンティティをエージェントに説明する Google のフォーマット | コーディングエージェントにビジュアルアイデンティティを記述するためのフォーマット仕様。DESIGN.md はエージェントにデザインシステムの永続的で構造化された理解を与える | 組織（google-labs-code）・Apache-2.0 | 28,957 |
| 10 | [meta/astryx](https://github.com/meta/astryx) | Meta のオープンソースデザインシステム。エージェント向けに作られた | 完全にカスタマイズ可能でエージェント対応（agent ready）のオープンソースデザインシステム | 組織（meta）・MIT | 30,755 |
| 11 | [google-labs-code/stitch-skills](https://github.com/google-labs-code/stitch-skills) | プロンプトを UI 画面に変える Google Labs のスキル | Stitch MCP サーバーと組み合わせて使う Agent Skills のライブラリ。各スキルは Agent Skills のオープン標準に従い、Antigravity・Gemini CLI・Claude Code・Cursor などのコーディングエージェントと互換 | 組織（google-labs-code）・Apache-2.0 | 10,554 |
| 12 | [GLips/Figma-Context-MCP](https://github.com/GLips/Figma-Context-MCP) | Figma のレイアウトをそのままエージェントへ | Cursor などの AI コーディングエージェントに Figma のレイアウト情報を渡す MCP サーバー | 個人（GLips）・MIT | 16,261 |
| 13 | [excalidraw/excalidraw](https://github.com/excalidraw/excalidraw) | 手描き風の図とワイヤーフレーム | 手描き風の図をスケッチするバーチャルホワイトボード | 組織（excalidraw）・MIT | 135,942 |
| 14 | [tldraw/tldraw](https://github.com/tldraw/tldraw) | 無限キャンバスの SDK | tldraw SDK で React の無限キャンバスアプリを作る。「世界最高・エージェント推奨 No.1 の五つ星 SDK」（description の自己紹介文をそのまま訳） | 組織（tldraw）・GitHub API のライセンス欄は NOASSERTION（独自表記） | 51,492 |
| 15 | [danielgatis/rembg](https://github.com/danielgatis/rembg) | 1 行で背景除去 | 画像の背景を除去するツール | 個人（danielgatis）・MIT | 25,579 |

投稿者は分類を示していない。並び順から読むと、1〜5 が UI 部品・アイコン・配色の素材、6〜12 がエージェントに渡す設計知識と接続（スキル・仕様ファイル・デザインシステム・MCP）、13〜15 が作図と画像処理の道具にあたると考えられる（wiki 側の推論）。

## 投稿者の一言と GitHub 記載の照合

- **エージェント向けに作られたのは 7 項目（6 リポジトリ）**。ui-ux-pro-max（AI スキル）・frontend-design と theme-factory（Agent Skills）・DESIGN.md（エージェント向け仕様）・Astryx（agent ready）・stitch-skills（Stitch MCP 前提の Agent Skills）・Figma Context MCP（MCP サーバー）は description 自体がエージェントからの利用を前提にしている。残る 8 本（shadcn/ui・Tailwind CSS・Magic UI・Lucide・Leonardo・Excalidraw・tldraw・rembg）は人が使う汎用のライブラリとアプリで、一覧の半分強はエージェント以前から定番の道具である。
- **2 項目は同じリポジトリ**。7 本目と 8 本目はどちらも anthropics/skills の中のディレクトリで、スターの 197,472 はリポジトリ全体の数であり、2 本のスキルの人気ではない。anthropics/skills はリポジトリのライセンス欄が未設定で、各スキルの利用条件は中を見る必要がある。
- **数字の食い違い**。投稿者は ui-ux-pro-max を「80 の UI スタイル」と書くが、[[tools/ui-ux-pro-max]] が README から転記した数は 79 スタイル（2026-08 時点）。差 1 はバージョン差か丸めかは未確認。
- **Figma Context MCP は個人製**。所有者 GLips は個人アカウントで、[[tools/claude-code-mcp-stack]] が構成に挙げる Figma の MCP サーバー（同ページは公式 MCP サーバー 8 本として整理）とは別物と考えられる。description も「Cursor などの AI コーディングエージェント向け」と第三者製の立場で書かれている。
- **更新の鮮度に差がある**。Leonardo の最終 push は 2026-07-08 で、他の 13 本（2026-11-06 から 19 の間）より 4 か月空いている。tldraw の description は自分で「エージェント推奨 No.1」と名乗る宣伝文で、リポジトリ側の自己申告である。

## 既存ページとの関係（wiki 側の推論）

- 15 項目のうち wiki に独立ページがあるのは [[tools/ui-ux-pro-max]] と [[design/design-md]] の 2 本。frontend-design スキルは [[tools/ui-skills]] のカタログ行と [[design/claude-premium-website-build]] のワークフローに登場し、同カタログの `shadcn` スキル（shadcn-ui 作）は 1 本目の shadcn/ui 本体を扱うスキルである。shadcn/ui 本体のページは無く、取得した README は冒頭の説明（「自分のコンポーネントライブラリを作るために使う」・MIT）だけなので、本ページの行に留めた
- AI 製 UI の凡庸さ対策として wiki にある [[tools/impeccable]]（frontend-design スキルから派生した決定論検出器）・[[tools/unslop]]・[[tools/taste-skill]] はこの一覧に入っていない。一覧は「凡庸さを検出して直す」側より、「良い部品と仕様を先に渡す」側に寄っている
- DESIGN.md（仕様を書いて渡す）・Astryx（出来合いのデザインシステムを渡す）・stitch-skills（プロンプトから画面を起こす）は、エージェントにデザインを与える 3 つの経路として並べられている。[[design/design-md]] が frontend-design との役割分担（禁止ルールか参照仕様か）を問いにしているのと同じ線が、Astryx と stitch-skills にも引ける
- 同じ「リポ N 選」の投稿形式では [[tools/creative-pipeline-repos]] が映像制作の工程別、[[tools/agent-skills-by-role]] が職種別、本ページが UI の素材から接続までの層別にあたる

## 問い

- 独立ページがない 12 本のうち、エージェント用途で README を読む価値が高いのは Astryx（Meta のエージェント対応デザインシステム）と stitch-skills（Stitch MCP サーバー前提）。DESIGN.md が「仕様を書く」なら Astryx は「システムを渡す」別解か、重ねて使うものか
- 「紫グラデーションなし・汎用カードなし」をこの 15 本のどれが保証するか。部品ライブラリ（shadcn/ui・Magic UI）の既定値はそのまま使うと「AI っぽい」になりうる。一覧を貼って作った画面を [[tools/impeccable]] の 62 ルールで検出させると何本引っかかるか

## 関連

- [[tools/ui-ux-pro-max]] — 一覧の 6 本目。79 スタイル・192 配色・74 フォントペアを内蔵しデザインシステムを生成する Agent Skill
- [[design/design-md]] — 一覧の 9 本目。Google の DESIGN.md 仕様と、有名サービスの仕様を配るカタログ
- [[tools/ui-skills]] — frontend-design・ui-ux-pro-max・`shadcn` スキルを掲載する UI スキルのカタログ
- [[design/claude-premium-website-build]] — frontend-design と UI/UX Pro Max を併用する制作ワークフロー
- [[tools/impeccable]] — frontend-design スキルから派生した「AI スロップ」の決定論検出器。一覧には無いが同じ問題を扱う
- [[tools/unslop]] — 「AI っぽい UI」の 7 群の兆候（紫グラデーション等）を監査して直すスキル。一覧の枕と同じ語彙
- [[tools/taste-skill]] — anti-slop 特化のスキル集。一覧の「良い部品を先に渡す」に対し「出力を調整する」側
- [[tools/claude-code-mcp-stack]] — Figma など公式 MCP サーバー 8 本の構成論。一覧の Figma Context MCP は個人製の別実装
- [[tools/creative-pipeline-repos]] — 同じ形式の映像制作 10 リポ一覧（Florence）。本ページは UI 版
- [[tools/agent-skills-by-role]] — 職種別のリポ 6 選。本ページは素材から接続までの層別
