# HTMLを柔軟な出力フォーマットとして使う

Anthropicが公開した実例ギャラリーリポジトリ「html-effectiveness」。ブログ記事「The unreasonable effectiveness of HTML」に付随し、LLMの**出力**フォーマットとしてHTMLを使う設計思想を、ビルド不要・依存なしの自己完結型HTMLファイル群で示す。各ファイルは単体の`.html`で、ブラウザで直接開くだけで動く。

## 収録カテゴリと用途

| カテゴリ | 用途例 |
| --- | --- |
| Exploration | コードアプローチ比較、ビジュアルデザイン比較 |
| Code | レビュー、理解支援、デザインシステム、コンポーネントバリアント |
| Prototyping | アニメーション、インタラクション |
| Communication | スライドデック、ステータスレポート、インシデントレポート、PR説明 |
| Diagrams & research | フローチャート、機能・概念の説明図 |
| Custom editing UIs | トリアージボード、機能フラグ管理、プロンプトチューナー |

サンプル内の製品名・データ・シナリオはすべて架空のもの（プレースホルダーブランド「Acme」など）で、説明目的のみに使われている。

## 問い

- ブログ本文「The unreasonable effectiveness of HTML」がHTML出力を推す具体的な理由（Markdownやプレーンテキストとの比較根拠）を読む
- このパターンと[[concepts/llm-doc-management]]（LLMへの入力としてHTMLを使うパターン）を組み合わせ、入出力の両方をHTML+JSONで統一できるか試す

## 関連

- [[concepts/llm-doc-management]] — 同じHTML活用でも逆方向：LLMへの入力（コードベース文書化）としてHTML+JSONを使うパターン
- [[design/claude-design-workflow]] — HTML視覚化を制作物のデザイン叩き台に使う実践
- [[tools/hyperframes]] — HTML+data-*属性を決定論的な動画生成の入力として使う応用例
