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

## 実践者の運用例: 長時間タスクの前にダッシュボードを作らせる（@Voxyz_ai）

@Voxyz_ai は、[[models/claude-opus-5-5]] に長いタスクを任せるときは毎回、まず簡単な HTML ダッシュボードを vibe-code させるよう勧めている。そのうえでダッシュボード作成だけを担当するサブエージェントを用意し、名前を `dashboard-builder`、effort を medium に設定すると述べる。ポスト本文はここで途切れ、続きの設定とダッシュボードの見本は画像のため未捕捉。自律実行の途中経過を人間が読める形に出させる、という点で Communication カテゴリの「ステータスレポート」を常駐させる使い方と読める（wiki側の解釈）。

## 問い

- ブログ本文「The unreasonable effectiveness of HTML」がHTML出力を推す具体的な理由（Markdownやプレーンテキストとの比較根拠）を読む
- dashboard-builder サブエージェントの定義本文（ツール・出力先・更新頻度）は何か。途切れた続きと画像の捕捉待ち
- このパターンと[[concepts/llm-doc-management]]（LLMへの入力としてHTMLを使うパターン）を組み合わせ、入出力の両方をHTML+JSONで統一できるか試す

## 関連

- [[concepts/llm-doc-management]] — 同じHTML活用でも逆方向：LLMへの入力（コードベース文書化）としてHTML+JSONを使うパターン
- [[design/claude-design-workflow]] — HTML視覚化を制作物のデザイン叩き台に使う実践
- [[tools/hyperframes]] — HTML+data-*属性を決定論的な動画生成の入力として使う応用例
- [[tools/diagram-design]] — HTML+SVGだけで27種類の図を出しブランドの色・フォントまで寄せるリポジトリ。図版をHTMLで作る応用例
- [[tools/show-me]] — Dex Horthyによる対案。HTMLより軽いコンポーネント木・コールスタック・型シグネチャを日常の応答形式にし、HTMLはモックアップと説明図に限定する
- [[tools/html-share]] — 出力したHTMLの置き場所と配布経路（自分専用の一覧・期限付き共有URL・スマホでの承認）をセルフホストで用意するツール
- [[tools/slack-html]] — 自己完結HTMLをSlackに添付してアプリ内で展開する配布経路（@geeorgeyの造語「SlackHTML」）。単一ファイル・依存なしという本リポジトリの前提がそのまま条件になる
- [[tools/claude-code-subagents]] — dashboard-builder のような出力専用サブエージェントの定義方法
- [[models/claude-opus-5-5]] — 長時間タスク前にダッシュボードを作らせる運用の対象モデル（@Voxyz_ai）
- [[tools/html-slide-presenter]] — HTMLスライドの横に presenter.html を置き、元HTMLを変えずに発表者ツールを外付けする応用例（梶谷健人製）
- [[tools/html-plan]] — HTML 出力を実装プランの質疑に絞った実装例（@trq212 作）。主張の木を1階層ずつ開き、回答を1回の貼り戻しで返す
