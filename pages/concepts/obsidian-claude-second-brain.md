# Obsidian + Claude 第二の脳

> **TL;DR**: Obsidianを単なるノートアプリ、Claudeを単なるチャットボットとして別々に使うのをやめ、両者を「統合レイヤー」で接続すると、自分の全知識を文脈に持つAI第二の脳になる。鍵はプラグイン選定でも個別ワークフローでもなく、vaultをAIの永続メモリに据える設計にある。

多くの人がObsidianとClaudeを**別々のツール**として使うため、Claudeは毎セッション知識ゼロから始まり、Obsidianの蓄積は検索されるだけの死蔵データになる。両者を繋ぐと、Claudeは過去のノート全体を参照して回答し、その出力をまたvaultへ書き戻すため、使うほど知識が複利で積み上がる。導入の構造は3層に整理できる。

**① 基盤プラグイン（vaultをAI可読にする）**: Smart Connections / Copilot（RAGでvault全体と対話）、Templater（ノートを常に構造化＝AI検索精度が上がる）、Dataview（vaultをクエリ可能なDB化）、Tasks（全ノート横断のタスク把握）、Periodic Notes/Calendar（時系列の記録）、Obsidian Git（履歴と復元）、そして2026年リリースの**Obsidian CLI**（Claude Codeがコマンド経由でvaultをプログラム操作できる接続点）。

**② ワークフロー（人間の定型作業をClaudeに渡す）**: 朝のシンセシス（直近の日次ノート＋進行中プロジェクトから「今日の起点」ノートを生成）、会議プロセッサ（生メモ→アクション項目・決定事項・リンク・タグ）、リサーチ取り込みパイプライン（URL/PDF→要約＋既存ノートへのリンク＋矛盾検出）、週次レビュー自動化、アイデア相互受粉（非自明なノート間接続の発掘）、vault健全性チェック（孤立ノート・陳腐化・停滞プロジェクトの監査）など。

**③ 上級セットアップ（vaultをAIの脳に固定する）**: `CLAUDE.md` にvaultを長期記憶として定義する、mcpvault等のMCPサーバでObsidian非起動でもvaultを読ませる、kepano（Obsidian CEO）公式スキルやobsidian-second-brainスキルを使う、夜間Routineでinbox自動整理、Zettelkasten×AI（原子的ノートのリンク鎖をAIが辿って推論）、Claude.ai Projectsへvaultの一部を知識ファイルとして渡す、AI出力を`#ai-generated`で書き戻すフィードバックループ。

最小実用構成は「6プラグイン＋1 MCPサーバ＋2自動化」程度で組め、PARAフォルダ構造・日次ノート習慣・最初のMCP接続の3ステップから始めるのが定石。ボトルネックは技術ではなく初期セットアップを越える最初の数時間にある。

## 観察ログ（未検証）

- 2026-05-31: 出典は@eng_khairallah1の「30 Obsidian Workflows, Plugins, and Setups」listicle（AIコース系アカウント・X article）。Obsidianのコミュニティプラグインは2,700+、うちAI関連100+と主張
- 2026-05-31: kepano（Obsidian CEO）公式Claude Skillsが「3ヶ月未満でGitHub 12,900+ stars」と記載（→ [[tools/obsidian-skills]] の数値更新材料）。obsidian-second-brainスキルは「31コマンド」、mcpvaultは「ゼロ依存・BM25検索・14 MCPメソッド」と紹介
- 2026-05-31: 著者の常用スタック＝Obsidian（Smart Connections/Templater/Dataview/Periodic Notes/Tasks）＋mcpvault＋CLAUDE.md＋夜間Routine＋金曜週次レビュー。「セットアップに半日、毎週数時間を節約」と主張（単一ソース・効果は未検証）
- 二次まとめlisticleのため、個別プラグインの機能記述は一次確認推奨。本ページは「統合レイヤーという設計思想」の合成に重心を置き、個別ツールの数値は観察ログに留める
- 2026-06-06: 同著者@eng_khairallah1の別article（@undefinedKiが紹介、bookmark 4,671）が「Obsidianをジャービス化し、深夜3時のアイデアを寝ている間に完成プロジェクトとして出荷するパイプラインを組んだ」と主張。「作りたいアイデアの数 ≫ 作る時間」という問題を、生アイデア→完成物まで運ぶパイプラインで解いた、という枠組みのみで具体手法は当該紹介ポストには未記載（teaser・手法本体は元article）。「統合レイヤー＝vaultをAIの永続メモリに据える」設計の発展形＝自律出荷パイプラインの方向を示す傍証として記録

## 問い

- このlisticleの30項目のうち、自分のllm-wikiシステム（ingest/lint/Weekly Insights）に既にあるもの・無いものはどれか。「vault健全性チェック」は `scripts/validate_wiki.py` と重複/補完どちらか
- mcpvault等のMCPサーバ導入は、現在のClaude Code直接ファイル操作より速いか。BM25検索は `wiki/index.md` 手動カタログより有効か
- 「AI出力を書き戻すフィードバックループ」は、根拠なし追記禁止（sources必須）の本vault規約とどう両立させるか

## 関連

- [[concepts/obsidian-personal-os]] — Obsidian+Claude Code+N8Nの3層で「崩壊しないパーソナルOS」を設計する手法（こちらはアーキテクチャ重心、本ページはプラグイン/ワークフロー網羅重心）
- [[concepts/obsidian-second-brain-setup]] — 同領域の「install→autopilot 構築手順」重心。本ページのワークフロー網羅に対し、配線手順とプロジェクト構造の設計判断に絞った姉妹ページ
- [[concepts/llm-wiki]] — Karpathy提唱のLLMが継続メンテする知識ベース（本vault自体の設計思想）
- [[tools/obsidian-skills]] — kepano製Obsidian向けAgent Skills集（listicleで言及される公式スキル）
- [[tools/claude-code-obsidian-project]] — Claude Code+Obsidian「何でも相談」プロジェクトの実践設計例
- [[tools/claude-code-obsidian-sync]] — Claude Code会話履歴をObsidianに自動記録する実装
- [[concepts/output-first-learning]] — 書いて蓄積する側の「学習法」（出力を強制関数に知識を血肉化）。同一著者@eng_khairallah1繋がりの別概念は [[concepts/claude-projects-blueprint]]
- [[concepts/frontier-model-extraction]] — deep researchを原子ノート化してvaultへマイニングする型3の実践先（「要約でなく原子化せよ」）
- [[concepts/second-brain-operations]] — 同領域の「維持ループ×コスト設計」重心の姉妹ページ（raw/entities/concepts/INDEXの4ピース構造・@EXM7777）
- [[concepts/personal-intelligence-os]] — 同領域の姉妹ページ。検索起点設計・Knowledge ROI・Decision Note第一級化が重心（@ai_ai_ailover）
