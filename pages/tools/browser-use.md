# Browser-use

WebブラウザをAIエージェントが操作するためのライブラリ。LLMがブラウザを直接制御してウェブスクレイピング・フォーム入力・情報収集などを自動化する。

## 特徴

- [[tools/cursor]] の Roo-Cline 拡張から自動導入が可能
- DeepSeek V3 と組み合わせると API コストが1円以下
- 依存関係含めて自動インストールが完了する

## 導入方法（Roo-Cline 経由）

1. Roo-Cline を起動
2. API に DeepSeek V3 を設定
3. リポジトリのURLを渡して「導入して」と指示するだけ

## 関連

- [[tools/cursor]]
- [[concepts/agentic-coding]]
- [[tools/claude-computer-use]]（AnthropicによるデスクトップUI操作エージェント機能）
- [[tools/video-use]] — 同じ「◯◯-use」命名系譜の Claude Code 動画編集 OSS
- [[tools/jev-ultrafast]] — 同じ browser-use による高速化版の別リポジトリ。番号付き要素テーブルと、操作・対象を1往復で決める構成
- [[tools/1password-for-agents]] — 1Password が 2026-10-22 に発表したサインイン承認レイヤー。Browser Use は追加連携予定の企業として挙がる。ブラウザ操作エージェントがログイン画面で止まる問題への外部からの解
