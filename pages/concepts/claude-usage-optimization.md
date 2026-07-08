Claudeのトークン使用制限（レートリミット）を回避するための実践的フレームワーク。@milesdeutscher が5ステップとして整理したもので、計画優先・チャット長管理・モデルスタッキング・記憶管理・ツール分割で構成する。関連: [[tools/claude-code]]・[[concepts/prompt-engineering]]

トークン消費はテキストチャットよりも描画・コーディング・実装作業で圧倒的に多くなると @milesdeutscher は観察し、そこから5つの節約策を導く。

- **計画優先**: 計画フェーズを Haiku で行い、実装フェーズのみ Opus に切り替えてコストを抑える。
- **チャット長管理**: 長いチャットは「古いコンテキストを毎回再読み込みさせる」ためトークンを浪費し品質も落ちる。1つの長いチャットより3つの独立チャットの方が効率的で、Projects 機能でサブチャットを整理し新タスクは必ず新チャットで開くよう同氏は推奨する。チャットを切り替える際は「このセッションをコンテキストを失わずに再起動できるプロンプトを出力して」と依頼する「Mega Prompt」手法で引き継ぐ。
- **モデルスタッキング**: 軽タスクは Haiku、中タスクは Sonnet、重タスク・最終仕上げは Opus へエスカレーションする（[[concepts/llm-model-selection-strategy]]）。
- **記憶管理**: ルール・指示を書く Instructions.md と、継続更新する Memory.md の2ファイルを Claude Code / Cowork にアタッチする。
- **ツール分割と補助設定**: Extended/Adaptive Thinking は大半のタスクで OFF、Concise スタイルでトークンを節約する。プラン更新（$20→$100→$200）より追加クレジット購入の方がコスパの良いケースもあると同氏は指摘する。

## 観察ログ（未検証）

- 2026-04-29 @milesdeutscher: $200/月プランでも使用上限に達していたが、5ステップ導入後3週間ゼロ上限を達成したと主張（単一ソースの効果報告）
- 2026-04-29 @milesdeutscher: 計画をHaiku・実装のみOpusに切り替えると vibe-coding アプリ例で最大67%コスト削減できるとの試算（単一ソースの数字）

## 検証済み事実

- 2026-04-29: Claude Code に Plan Mode（Shift+Tab×2 または `/Plan`）が存在し、計画フェーズ専用モードとして機能する
- 2026-04-29: Claude Code に `/Usage` コマンドが存在し、現在の使用量確認が可能（Overview セクション追加済み）
- 2026-04-29: Claude Code/Chat と Claude Design はトークン使用量が別カウント（異なる利用枠）

## 関連

- [[tools/claude-code]]（Plan Mode・モデル切替・設定詳細）
- [[concepts/prompt-engineering]]（プロンプト設計・最適化）
- [[concepts/claude-code-large-codebase]]（大規模コードベースでのコンテキスト管理）
- [[models/claude-opus-4-8]]（Effort Control・Fast Modeによるコスト最適化）
- [[concepts/llm-model-selection-strategy]]（工程分解型モデル選択：上流大モデル・下流小モデルのサンドイッチ戦略）
- [[tools/codexbar]]（Codex/Claude Codeの使用制限残量をメニューバー常駐で可視化するアプリ・steipete製）
