# SLIDE.md｜AI時代のスライド専用デザインシステム

> **TL;DR**: Claude Design の「スライドは爆速で作れるが毎回デザインがガチャのようにバラつく」問題を、デザインシステム（SLIDE.md）＋構図テンプレ99種（SLIDE-PATTERN）＋設計書（SLIDE-DECK.md）の3層 Markdown と専用スキル群で固定するスライド制作システム。

SLIDE.md が成立するのは「デザインの一貫性は感性でなく仕様で担保できる」という前提だ。色・フォント・余白・構図といった視覚要素を Markdown に明文化し、生成のたびに同じ仕様を参照させることで出力のバラつきを構造的に抑える。役割の異なる3層に分かれる。(1) **SLIDE.md** がブランドの基本デザインシステム（配色・タイポ・余白）、(2) **SLIDE-PATTERN-{name}.md** が個々のスライドの構図テンプレ（99種・`image-left-text-right` など）、(3) **SLIDE-DECK.md** がプレゼン1本分の設計書で、デザインシステム・選んだパターン・各スライドのコンテンツひな型を1ファイルに束ねる。最終的にこの SLIDE-DECK.md を Claude Design へ渡すとスライドが生成される。CLAUDE.md/AGENTS.md がエージェントの行動を、[[design/design-md]] がアプリのデザイン参照を `.md` で固定するのと同じ発想を、スライド制作に持ち込んだもの。

## セットアップと4ステップ運用

前提は Claude Code（有料プラン or API アクセス）と Claude Design（Claude 有料プランに同梱）。インストールは Claude Code に話しかけるだけで、3つのスキル（`slide-md-creator` / `slide-pattern-creator` / `slide-deck-builder`）・4種のサンプルデザインシステム・99種のスライドパターンが自動でセットアップされる（うまくいかなければ GitHub から手動コピーも可）。運用は4ステップ。

1. **インストール**（初回のみ）
2. **デザインシステムを用意** —— サンプル4種から選ぶか、`slide-md-creator` にスライド画像・PowerPoint・参考 URL のいずれかを渡してオリジナル生成（色・フォント・余白・雰囲気を自動分析し、読み取れなかった点だけ3〜5問確認）。各フォルダの `sample.html` で仕上がりを6ページ確認できる
3. **SLIDE-DECK.md を生成** —— ここが核。`slide-deck-builder` がブリーフ4問 → プレゼン内容（テキスト/md/pdf）→ デザインシステム選択 → 構成案確認 → 各スライドへのパターン割当確認、を経て設計書を生成する
4. **Claude Design で生成** —— `claude.ai/design` で Slides を選び、生成した SLIDE-DECK.md をアップロードするとデザインシステムとパターン定義を読み取ってスライドが出力される

2回目以降は Step 3 から始められる。出力は **PDF**（推奨・デザイン通りに出る）・**HTML**（フルスクリーンでそのままプレゼン・オフライン可）・**PowerPoint**（デザインが崩れる場合があり非推奨）。SLIDE-DECK.md 自体は NotebookLM や ChatGPT などどの AI ツールにも渡せるが、ビジュアル品質は Claude Design が最も高いとされる。

## 観察ログ（未検証）

- 2026-06-22: @sho_ai_magic（SLIDE.md 作者）が実践ガイド（インストール〜生成）を公開。コンセプト編・構図コントロール編に続く第3回にあたる。「Claude Design は爆速だが毎回デザインがガチャのようにバラつくのが最大の悩み」だったのが開発動機と説明（X bookmark 1,400 / 2026-06-23 時点）
- 2026-06-22: 生成後の Mark up / Edit による微調整は「現時点では操作感があまり良くないため軽微な修正にとどめるのが現実的」で、それを解決するツールを別途開発中、と作者が述べる（単一ソース・本人の体感）
- 配布: GitHub `sho-ai-magic/slide.md`、パターンギャラリー `sho-ai-magic.github.io/slide.md`

## 問い

- SLIDE.md の「デザインを `.md` で固定」は [[design/design-md]]（アプリ版）と同じ発想。スライドとアプリでデザインシステム `.md` を共通化・相互流用できるか
- [[design/ai-slide-creation]] の「主張→構造→見た目」の方法論（中身）と SLIDE.md（見た目システム）を組み合わせれば、刺さる＋崩れないスライドになるか
- PowerPoint 出力が崩れる問題は [[design/ai-slide-to-pptx]] の pptx 変換手法で回避できるか

## 関連

- [[design/claude-design-workflow]] — 同じ Claude Design を制作主体に据えるワークフロー（こちらは意思決定パートナー化、SLIDE.md はデザインの固定化）
- [[design/design-md]] — デザイン仕様を `.md` でプロジェクトに常駐させる発想のアプリ版
- [[design/ai-slide-creation]] — スライドの「中身（主張・構造）」を固める方法論（SLIDE.md は見た目を担当し補完的）
- [[design/ai-slide-to-pptx]] — AI 生成スライドを編集可能な pptx に変換（PowerPoint 出力の崩れ対策）
- [[design/claude-premium-website-build]] — Claude で高品質制作する別領域のワークフロー
