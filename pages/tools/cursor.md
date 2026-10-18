# Cursor

**Cursor** はAI統合開発環境（IDE）。コード補完・生成・リファクタリングをインラインで実行できる。

## 主な機能

- AIによるコード生成・補完
- **Cursor SDK**: エージェントをCI/CDパイプラインや外部製品に組み込める
  - 同じランタイム・ハーネス・モデルで動作
  - エンドツーエンドのオートメーション構築が可能

## ベストプラクティス

プロジェクトの詳細コンテキストをシステムプロンプトに記述することで、AIのハルシネーションを85%削減できるとされる。

## 組み合わせワークフロー

- **Cursor × Roo-Cline × DeepSeek V3**: API コスト1円以下で [[tools/browser-use]] を自動導入
- **Cursor × CodeRabbit**: Cursor がコードを書き、CodeRabbit がレビュー、Cursor が修正という自律ループ（[[concepts/vibe-coding]] の実践例）

## Windsurf との比較

Cursor に似たAI IDE として **Windsurf** がある。どちらも詳細なコンテキスト提供によるハルシネーション削減が重要。

## 関連

- [[tools/claude-code]]
- [[tools/browser-use]]
- [[concepts/vibe-coding]]
- [[concepts/agentic-coding]]
- [[tools/understand-anything]]（コードベースを対話型ナレッジグラフ化する補助ツール・Cursor対応）
- [[concepts/cursor-instruction-methods]]（AGENTS.md/Rules/Commands/Skills/Subagentsの使い分けフレーム）
- [[tools/grok-bot]]（Cursorクラウドエージェントを起動・監視・催促する管理層ボット。private workerで自前Mac miniも使う・@lingxiの200超同時管理の運用記）
