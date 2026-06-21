# OpenClaw agent-skills

**[[tools/openclaw|OpenClaw]]プロジェクト向けの共有スキルリポジトリ**。「ワークフローを1度書けばどこでも再利用でき、長い`SKILL.md`を全リポジトリに手コピーしなくて済む」ことを目的とした公開正本（canonical source）。レビュークローズアウトやリモート検証など共通ワークフローを集約する。

## 同梱スキル

| スキル | 用途 |
|--------|------|
| `agent-transcript` | ローカル限定・秘匿化されたPR/issueトランスクリプトのprovenance管理 |
| `autoreview` | 構造化されたクローズアウト/コードレビューワークフロー＋ヘルパースクリプト（→[[tools/autoreview-skill]]） |
| `crabbox` | [[tools/crabbox|Crabbox]]/Testboxでのリモート検証ワークフロー（広域・CIパリティの証明） |
| `handoff` | パス非依存のプロンプトハンドオフ。別エージェントへのタスク委譲 |
| `session-viewer` | エージェントセッションJSONLのローカル検索可能HTMLビューア |

**製品固有スキルはそのリポジトリに留める**設計思想（例: `acpx`の使い方スキルは`openclaw/acpx`に、汎用レビューヘルパーはここに）。

## インストール方式（symlink vs copy）

`scripts/install-skills` で導入。`--list` / `--dry-run` / 個別指定 / `--target` / `--mode copy` / `--force` をサポート。

- **symlink**: チェックアウトの変更が即反映。ローカル開発向き
- **copy**: ポータブル・ロックダウン環境向き
- Codex: `~/.codex/skills` に、Claude Code: `~/.claude/skills` にsymlink。既に別の共有フォルダを指している場合はそのフォルダ内に個別symlinkを追加

## Zero-Setupリポジトリとvendoring

クローンしただけで共有スキル未インストールの貢献者でも動くべき重要リポジトリは、生成済みスナップショットを`.agents/skills/<name>`に**vendor**できる。ただしスナップショットは「配布アーティファクトであって正本ではない」：

- 正本スキルはここで先に編集 → レビュー後に下流スナップショットへ同期
- 下流コピーは数を絞る／provenance・driftチェックを付ける
- **手作業で下流スナップショットを更新しない**（このリポを更新してから同期する）
- `autoreview`はレビュークローズアウトが貢献ワークフローの一部なので、フラッグシップリポのzero-setpスナップショット候補として良い

> 設計の核: 共有スキルの「正本は1箇所・配布はsymlink/vendorで多重化」という二層構造。[[concepts/skill-building-best-practices]]の「マーケットプレイスで配布」を、軽量なgit symlink方式で実現した実装と言える。

## 編集ルール

- descriptionはルーティング用に短く有用に保つ（[[concepts/skill-building-best-practices|description＝トリガー定義]]と同じ思想）
- スキル本文はエッセイ調でなくオペレーショナルに
- secret・プライベートホスト名/アカウントID/URLを含めない
- 反復コマンドはヘルパースクリプトに切り出す
- `session-viewer`のHTML出力は秘匿化・レビュー前提でローカル/プライベート扱い

## 観察ログ（未検証）

- 2026-06-05: バリデータ`scripts/validate-skills`は各`skills/*/SKILL.md`のYAML frontmatterと必須の`name`/`description`を検査する（複数言語のスクリプトをRuby/Python/Node/bashでcheck）
- 2026-06-05: @hungv47 が「このリポがスター400未満なのは犯罪的」と推薦し、`/autoreview` と `/handoff` を「これまで使った中で最高のagent skill 2つ」と評価（@steipete・@openclaw への謝辞）。当時の注目度は実利用者からの評価に対し相対的に低かったとの観察
- 2026-06-11: @steipete（OpenClaw作者）が `maintainer-orchestrator` スキルをGitHubに公開。steipeteのGitHubには合計49個以上のスキルが存在し、`triage` + `autoreview` + `computer use` と組み合わせた「5分ごとに起動してリポをメンテし並列スレッドに作業を振る[[concepts/loop-engineering|ループ]]」の骨格が提示された（@masa_okamura108 経由）

## 問い

- このwikiの`.claude/skills`も同じ「正本1箇所＋symlink配布」パターンを取れるか。現状はリポ直チェックインだが、複数vault/環境で再利用するならsymlink正本化が効くか
- vendoringのdriftチェック（正本とスナップショットの差分検出）を自分のスキル運用に取り入れる価値はあるか
- [[tools/skill-cleaner]]（OpenClaw系の監査CLI）と組み合わせると正本リポの品質ゲートを自動化できるか

## 関連

- [[tools/openclaw]] — 親プロジェクト（OSS AIエージェント基盤）
- [[tools/autoreview-skill]] — 同梱の主力スキル（コードレビュー自動化）
- [[tools/crabbox]] — 同梱のリモート検証ワークスペース制御
- [[tools/skill-cleaner]] — Codex/OpenClawスキルの監査CLI（重複・未使用検出）
- [[concepts/skill-building-best-practices]] — スキル構築・配布の一般原則（本リポはその配布層の実装例）
- [[concepts/cursor-instruction-methods]] — AGENTS.mdでの共有スキル参照を含む指示手段の使い分け
