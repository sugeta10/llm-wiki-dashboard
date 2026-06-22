> **TL;DR**: 技術勉強会のLT（ライトニングトーク）を申し込んで発表するための実践的リソースガイド。ツール選定・素材調達・発表後の公開まで網羅。

まず申し込む「えいやっ」が最初の障壁。LTは完璧さより発表することに価値がある。主催者にとって発表者の確保がイベント成功の鍵であり、たとえ失敗しても"えらい"。発表後はSpeakerDeck等への公開が定着への近道。LTとは60〜90分の枠に5分トークを詰め込む形式で、シリアスに考えすぎず「気軽に伝えたいことを話して急いで降りる」のが本来の精神。

## 観察ログ（未検証）

- 2026-06-10: @moomooya（Qiita）が社内向け参考資料をまとめて公開。不定期更新中
- 2026-06-10: ウケるLTの傾向として「シンプルな内容・時事ネタ・面白いデモ・予想外の展開」が挙げられている
- 2026-06-10: スライド公開先としてSpeakerDeck（GitHub連携）が主流になりつつあるとのこと

## プレゼンツール

| ツール | 特徴 |
|--------|------|
| **Marp** | Markdown(CommonMark)→スライド生成。HTML/PDF/PPTX出力対応。VSCodeプラグインで使いやすい |
| **Slidev** | Markdown→スライドCLI。シンタックスハイライト優秀。カメラ映像・録画機能あり |
| **Reveal.js** | HTML+MarkdownでPreziライクなスライド |
| **impress.js** | HTMLファイルでPreziライクな演出 |
| **Keynote / PowerPoint / Google Slides** | 標準ツール。Googleはそのまま公開可 |
| **Beamer** | LaTeX→PDF。学術発表向け |

## 素材リソース（フリー・利用制限が少ないもの）

- **いらすとや** — 汎用性高いイラスト。商用は21点以上有償
- **Unsplash / PIXABAY / Pexels** — 版権フリーストックフォト
- **unDraw** — ビジネス/IT向けイラスト、商用フリー
- **DOTOWN** — 元任天堂デザイナーのドット絵素材
- **carbon.now.sh** — シンタックスハイライト付きコード画像生成
- **Adobe Color CC** — 配色決定に便利

## 問い

- Marp/SlidevのAI連携（Claude Codeでスライド自動生成）は実用的か？→ [[design/ai-slide-to-pptx]] との比較
- LT発表後のSpeakerDeck公開によるネットワーク効果（採用・ヘッドハンティング等）をどう測定するか？

## 関連

- [[design/ai-slide-to-pptx]]
- [[design/fastview-design]]
