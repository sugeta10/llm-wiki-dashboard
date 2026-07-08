ClaudeにリアルUIを操作させるエージェント機能。マウスクリック・キーボード入力・スクリーンショット読み取りを通じてデスクトップ/ブラウザアプリを自律的に操作する。

## 観察ログ（未検証）

- 2026-05-19: @ClaudeDevs（Anthropic公式）がプロダクション信頼性向上のブログポストを公開。クリック精度・Thinking effortレベル選択・長セッションのコンテキスト管理・デモ録画のリプレイ機能が主なトピック

## 検証済み事実

- Anthropic公式がプロダクション向けComputer Use安定化ガイドを公開（2026-05-19）

## 関連

- [[tools/browser-use]] — WebブラウザをAIが操作する専用ライブラリ（クロスブラウザ対応）
- [[tools/claude-code]] — Anthropic製AIコーディングCLI（同じく自律的に操作するが対象はコードベース）
- [[concepts/multi-agent-patterns]] — マルチエージェント設計パターン（Computer Useをサブエージェントとして組み込む構成を含む）
- [[tools/claude-real-video]] — 動画を変化フレーム抽出＋文字起こしで機械可読化しClaudeに見せるOSS（デモ録画リプレイと同じ「動画を扱う」系譜）
