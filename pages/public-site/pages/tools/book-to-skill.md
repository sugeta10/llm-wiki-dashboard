# book-to-skill

> **TL;DR**: 技術書・ドキュメントを `/book-to-skill <path>` 一発で [[concepts/claude-skills|Claude Code スキル]] に変換し、「一度読んで忘れた本」を必要な時にオンデマンド参照できるようにするOSSツール。

本一冊をそのままコンテキストに載せるとトークンが膨れ、必要のない章まで常時抱え込むことになる。book-to-skill はこれを、コアメンタルモデルと章インデックスだけを起点に置き、本文は質問が来た章だけを引き込むオンデマンドロード構成へ変換することで回避する。`/book-to-skill <path>` 一発で、この分割済みスキルセットが自動生成される。

変換時には書籍を複数ファイルに分解し、トークン効率を最適化したスキル構成を生成する。`SKILL.md` がコアメンタルモデルと章インデックスを担い、本文の各章は `chapters/ch*.md` に分割される。この章ファイルは**オンデマンドロード**方式で、そのトピックを質問するまでトークンを消費しない。これにより本全体を常時コンテキストに載せることなく、必要な章だけを引き込める。

| ファイル | 役割 | トークン目安 |
|--------|------|------------|
| `SKILL.md` | コアメンタルモデル＋章インデックス | 〜4,000 |
| `chapters/ch*.md` | 章ごとのファイル（オンデマンドロード） | 〜1,000/章 |
| `glossary.md` | 全キーワード・章参照付きアルファベット順 | 〜1,500 |
| `patterns.md` | 全テクニック・アルゴリズム・設計パターン | 〜2,000 |
| `cheatsheet.md` | 意思決定表・クイックリファレンス | 〜1,000 |

入力フォーマットは幅広く、PDF・EPUB・DOCX・TXT・Markdown・reStructuredText・AsciiDoc・HTML・RTF・MOBI/AZW/AZW3 に対応する。

## 使い方

```
/book-to-skill <path-to-document> [skill-name-slug]

# 例
/book-to-skill ~/Downloads/designing-data-intensive-applications.pdf
/book-to-skill ~/books/clean-code.epub clean-code
```

インストール後はスキルとして呼び出す:

```
/designing-data-intensive-apps                  # コアメンタルモデルをロード
/designing-data-intensive-apps replication      # トピック検索
/designing-data-intensive-apps ch05             # 第5章を詳読
```

## PDF抽出ツールの選択基準

入力PDFの中身によって抽出ツールを使い分ける。散文中心で数式やコードが少ない書籍なら軽量・高速な `pdftotext`（poppler）で十分だが、コード・表・数式を含む技術書では Markdown の表やコードブロックを保持できる `docling`（〜1.5秒/ページ）が適する。

| 書籍種別 | 推奨ツール | 理由 |
|--------|----------|------|
| 文章中心（散文・数式少） | `pdftotext`（poppler） | 高速・軽量 |
| **技術書（コード・表・数式含む）** | **`docling`** | Markdown表・コードブロック保持、〜1.5秒/ページ |

## 観察ログ（未検証）

- 2026-05-22: @tom_doerr が「技術書をClaude Codeスキルに変換する」ツールとして紹介。
- 2026-05-31: @so_ainsight が「読書家必見」として再紹介。この時点で GitHub 2,700スター超え。「PDF/EPUBを放り込むだけで章構成・主要概念・パターンを抽出」と要約。

## 検証済み事実

- 2026-05-24: GitHub（virgiliojr94/book-to-skill）に公開。MIT License。

## 問い

- 数百ページの技術書を実際に変換し、章インデックス＋オンデマンドロードで本全体を読むより安くトピック検索できるか自分で計測する。
- [[concepts/self-refining-skills]] と組み合わせ、変換後のスキルを使用ログから自動改善できるか試す。

## 関連

関連: [[tools/claude-code]] · [[concepts/claude-skills]] · [[concepts/self-refining-skills]]
