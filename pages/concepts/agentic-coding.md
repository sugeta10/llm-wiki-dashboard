# Agentic Coding

コードを書くAIエージェントが自律的にタスクを遂行する開発スタイル。[[concepts/vibe-coding]] の発展形で、人間の介在なしにコードの生成・テスト・デプロイまでを行う。言語別の生成コスト・速度比較については [[concepts/ai-coding-lang-benchmark]] を参照。

## 特徴

- AIエージェントが計画・実装・検証を自律的にループ
- CI/CDパイプラインへの統合が可能
- 複数エージェントが並列で作業する構成も普及中

## 主要ツール

- [[tools/claude-code]] : [[companies/anthropic]] 製のエージェントCLI
- [[tools/cursor]] / Cursor SDK: エージェントをCI/CDや製品に組み込み可能
- [[tools/browser-use]] : WebブラウザをAIが操作するエージェントライブラリ

## 技術選定への影響

Agentic Coding の普及は技術選定の基準を変えつつある。AIエージェントが扱いやすい技術（ドキュメントが豊富・コード例が多い）が優位になる傾向がある。

## 長時間実行エージェントのハーネス設計

Anthropic 公式リポジトリ「cwc-long-running-agents」が提示する3つのプリミティブ：

1. **Default-FAIL contract**: エージェントは「成功を証明できなければ失敗」として動作する。曖昧な完了を防ぐ
2. **Fresh-context evaluator**: 各ループ反復ごとに新しいコンテキストで評価する。前の試行バイアスを排除
3. **Agent-maintained handoff**: エージェント自身が次のエージェントへの引き継ぎ情報を生成する

関連実装: [[tools/claude-harness]]

## 自己改善ループ（Codex 活用例）

「Codex に自律的な自己改善ループをさせる」プロンプトパターン：

> 「その出力に100%の自信がありますか？もしそうでなければ、全ての抜け漏れ（要改善点）をチェックして、新しい出力に100%の自信を持てるようになるまで改善のループを続けてください」

このパターンは [[concepts/self-refining-skills]] のスキルレベルの実装と組み合わせることで特に効果的。

## 関連

- [[concepts/vibe-coding]]
- [[concepts/self-refining-skills]]
- [[concepts/12-factor-agents]]
- [[tools/claude-code]]
- [[tools/claude-harness]]
- [[tools/claude-managed-agents]]（Managed Agents：Dreaming・Outcomes・マルチエージェント協調）
- [[concepts/developer-ai-collaboration]]（開発者×AIの役割分担哲学）
- [[tools/cursor]]
- [[companies/anthropic]]
- [[concepts/google-code-review]]（Google公開のコードレビュー指針・AIエージェントへの指示仕様としても活用可）
- [[concepts/ai-engineer-roadmap]]（2026年AIエンジニアが学ぶ・作る・スキップすべきことの整理）
- [[concepts/recursive-self-improvement]]（AIがAI開発を加速し後継機を建造する流れ・Claudeが80%のコードを書く内部データ）
- [[concepts/ai-dev-two-commands]]（ZOZOによる/dev-init+/dev-resumeの組織標準化・Claude Code×Codex批判的対話レビュー）
- [[concepts/finding-unknowns]]（unknownsの削減と計画をagentic codingの中核スキルと位置づける実装前・中・後の技法フレーム・@trq212）
