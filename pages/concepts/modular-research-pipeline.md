# モジュラー研究パイプライン（Research Monster）

> **TL;DR**: 実行エンジン（Claude Code）・分析エンジン（NotebookLM）・記憶層（Obsidian）を役割分担させ、ソースを差し替えるだけで任意トピックを調査できる研究ワークフロー。使うほど自分の思考スタイルを学習して精度が上がる。

調査を「タブを10個開いて手で読んでメモする」手作業から、単一コマンドで回るパイプラインに変える設計。要点は個別ツールの選択ではなく**層の分離**にある。各層が問題の別レイヤーを担当し、入力ソースだけを交換すれば構造はそのまま再利用できる。@monokern が提唱（セットアップ30分）。

## 4層の役割分担

| 層 | ツール | 担当 |
|---|---|---|
| 実行エンジン | [[tools/claude-code]] | コマンド実行・スキル呼び出し・ファイル管理・パイプライン全体のオーケストレーション |
| カスタマイズ層 | Skill Creator（CCプラグイン） | 自然言語でスキルを生成・インストール（プログラミング不要） |
| 分析エンジン | [[tools/notebooklm]] / [[tools/notebooklm-py]] | ソースを読み込み要約・インフォグラフィック・フラッシュカード・ポッドキャスト等を生成 |
| 記憶層 | Obsidian | 生成物をMarkdownで蓄積し、CCが読んで好み・思考様式を学習 |

**コスト構造上の利点**: 重い分析処理をNotebookLMにオフロードすると、計算はGoogleのインフラ側で走りClaudeトークンを消費しない。本文中の実例では10本のYouTube動画の調査が約6分、その大半がGoogleサーバー側の処理時間。

## モジュラー性が本質（差し替え可能なのはソースだけ）

YouTube検索はあくまで一例。Claude Codeがアクセスできるデータソース（PDF・Webページ・ローカルファイル・Google Drive等）に差し替えても、**パイプライン・分析層・記憶層の構造は不変**。クリプトエコシステムをホワイトペーパーで、新技術をカンファレンス動画で、市場動向を公開レポートで——用途が変わってもテンプレートは同じ。これは [[concepts/intermediate-notation-pattern]] や [[concepts/multi-agent-patterns]] の「構造を固定し中身を流す」発想と同じ系譜。

## Obsidianが「複利」を生む仕組み

単発の調査なら上記だけで完結するが、Obsidianに生成物を貯め続けると質的に別物になる。CCがvault内の蓄積を横断的に読み、リンク構造・繰り返し戻るトピック・好む出力形式を把握していく。vault内の `claude.md` がこの学習を明示化する設定ファイルで、「直近セッションから自分のパターンを抽出して更新して」と週1で頼むと、1ヶ月で過剰なプロンプトなしに望む出力が返るようになる、と主張される。記憶層としてObsidianを使う点は [[concepts/obsidian-personal-os]]・[[concepts/llm-wiki]] と同思想。

## 観察ログ（未検証）

- 2026-05-31: セットアップ時間「30分未満」・YouTube調査10本で「約6分」はいずれも著者の自己申告値
- 2026-05-31: 「1年継続すれば数百セッションを吸収した訓練済みアシスタントになる」は著者の予測（効果測定なし）
- 2026-05-31: notebooklm-pyはGoogle非公開APIを使う非公式ライブラリで予告なく壊れうる（[[tools/notebooklm-py]] 参照）

## 問い

- 自分の llm-wiki（このvault）の wiki-ingest フローは既にCC＋Obsidianの2層を持つ。NotebookLM分析層を足す価値はあるか、それともトークン節約のオフロード目的に限るか
- 「使うほど賢くなる」の核は週次の `claude.md` 自己更新ループ。これは [[concepts/self-refining-skills]] の LESSONS.md アプローチと何が違うか

## 関連

- [[tools/claude-code]]
- [[tools/notebooklm]]
- [[tools/notebooklm-py]]
- [[concepts/obsidian-personal-os]]
- [[concepts/llm-wiki]]
- [[concepts/self-refining-skills]]
