# minorun-marp-skill｜登壇スライドの型を規則と実測検査に落としたMarpスキル

> **TL;DR**: 登壇スライドのストーリー・図・黒地デザインの型を3つのスキルに分け、書き出したPDFとSVGを実測する検査スクリプトで守らせる Marp 用のスキル集（minorun365 製・Apache 2.0）。

作者の minorun365（Minoru Onda）は動機を「AIにスライドを書かせると、体裁は整うのに話が薄くなりがち」と書く。そこで自分の登壇資料を作るなかでエージェントへ繰り返し伝えてきた直し方を、規則（人が読んでもそのまま使える文書）と検査スクリプト（書き出した成果物を機械が測る）の2本立てに落とした、というのがこのリポジトリの成り立ちである。規約ファイルを毎回読ませて機械チェックで違反を取る構図は [[design/consulting-pptx-skill]] と同じだが、あちらが検査するのはPPTX/HTMLの中身の規約、こちらが検査するのは書き出したPDF・SVGの余白や文字サイズという実測値になっている。

## 3つのスキルと配布物

| 場所 | 内容 |
| --- | --- |
| `skills/slide-story` | つかみ、中扉、段階的な開示、見出しの文体、締め方、尺の見積り |
| `skills/slide-figures` | 図の情報量の絞り方、SVGの描き方、文字サイズの下限、挿絵の置き方 |
| `skills/slide-design-dark` | 余白の測り方、縦のバランス、黒地の配色、表とコードのデザイン、Marpの罠 |
| `theme/minorun-dark.css` | 黒地にシアンの Marp テーマ |
| `tools/` | 書き出したPDFとSVGを実測する検査スクリプト |
| `examples/sample` | 見本デッキ（`examples/broken` は検査が反応することを確かめる、わざと崩した版） |

Claude Code で使う場合は `skills/` の3つを自分のスキル置き場へコピーし、テーマと検査スクリプトはスライドを置くリポジトリへ `theme/` `tools/` ごとコピーする。話の組み立て（story）・図（figures）・黒地レイアウト（design-dark）をスキル単位で分けているため、必要な層だけ入れられる構成になっている。

## 検査は書き出した成果物を測る

READMEが挙げる検査スクリプトは5本で、いずれも Marp CLI で書き出した後のPDFかSVGを対象にする。

| スクリプト | 測るもの |
| --- | --- |
| `check-dark-margins.py` | 中身の下端と右端の空き |
| `check-dark-gaps.py` | 図や箱と、隣の本文の間隔 |
| `check-figure-text.py` | 図の中の小さい文字、箱の縁に詰まった文字 |
| `check-svg-box-fit.mjs` | SVGの文字が箱に収まっているか |
| `check-reuse-diff.py` | 流用したスライドの、見出しと図の対応 |

実行には Marp CLI と Google Chrome、poppler（`pdftoppm` `pdfinfo`）、mupdf-tools（`mutool`）、Python 3 と Pillow が要る。`check-svg-box-fit.mjs` は Marp CLI 同梱の puppeteer-core を借りるので、場所が違う環境では `MARP_NODE_MODULES` と `CHROME_PATH` で指定する。

この一式で目を引くのは `examples/broken` の位置づけで、わざと崩したデッキを同梱して「検査が反応すること」を確かめられるようにしてある。検査そのものが黙って通ってしまう状態を検出する備えであり、規約を書いて終わりにしない設計だと考えられる。流用スライドの見出しと図の対応を見る `check-reuse-diff.py` も、過去の資料を使い回したときに話と図がずれる（体裁は保たれるのに中身が噛み合わない）という、作者が最初に挙げた問題への対策にあたる。

## フォントと挿絵の扱い

テーマは丸ゴシック系を想定し、第一候補は Zen Maru Gothic、未導入の macOS ではヒラギノ丸ゴで表示される。作者の手元は有償の丸ゴシック書体のため、公開されている登壇資料とは字形が少し違うと明記されている。挿絵は「いらすとや」を使う前提だが画像は同梱されず、見本デッキを書き出す前に `examples/sample/fetch-illustrations.sh` で取得する（利用条件は各自で確認するよう書かれている）。ライセンスは Apache License 2.0（Copyright 2026 Minoru Onda）で、見本デッキの一覧画像に写る挿絵はその対象外。

## 問い

- 話の薄さは `slide-story` の規則で本当に埋まるのか。余白や文字サイズと違って機械で測れないため、検査スクリプトが効くのは図とレイアウトだけに見える。ストーリー側の検査をどう作るかが残る問い
- 黒地シアンのテーマ前提で書かれた `slide-design-dark` の余白・配色ルールは、白地のテーマへどこまで移せるか。テーマ非依存の部分（余白の測り方・縦のバランス）だけ抜き出せるか
- 書き出した成果物を実測する検査は、スライド以外（このvaultの `wiki/outputs/` に出すHTML）にも移せるか。レンダリング後を測る発想は [[concepts/html-output-format]] のHTML成果物と相性が良さそうに見える

## 関連

- [[design/consulting-pptx-skill]] — レビュー指摘を溜めた規約ファイルを毎回読ませ、機械チェックで違反を検出するClaude Codeスキル。こちらは規約を成果物の実測（PDFの余白・図中の文字サイズ）で検査する
- [[design/slide-md]] — デザインシステムを `.md` で固定してスライドのバラつきを抑える体系。あちらは生成前の仕様で縛り、こちらは生成後の実測で縛る
- [[design/lt-preparation-guide]] — LT準備のツール選定・素材調達ガイド。Marp を選んだ後の「話の組み立てと図をどう作るか」を埋めるのがこのスキル集
- [[tools/html-share]] — 同じ minorun365 製の、Claude Code の生成物をHTML化して共有するツール
