# skill-cleaner

**skill-cleaner**は、Codex/OpenClawのスキルを監査するNode.js製CLIツール。プロンプトバジェット超過・重複スキル・未使用スキルを検出し、整理を促す。steipete（Peter Steinberger）の [agent-scripts](https://github.com/steipete/agent-scripts) リポジトリ内のスキルとして公開。

## 主な機能

- **Skill Budget分析**: GPT-5.5コンテキストサイズの2%をスキル予算として、どのスキルが何トークン消費しているか可視化
- **重複検出**: 同名スキルや内容が近似したスキルをCodex本体・プラグインキャッシュ・リポジトリ・個人スキルルート横断で検出
- **未使用候補の検出**: 最近のCodex/OpenClawログを解析し、一定期間使われていないスキルを特定
- **説明文の最適化候補**: 長すぎる description を検出し、文法緩和でバジェット削減できる箇所を提案
- **ルートサマリー**: スキルの出所（Codexシステム/プラグイン/個人）と有効/無効状態の一覧

## 実行方法

```bash
# 基本実行（3ヶ月のログを対象）
node --experimental-strip-types skills/skill-cleaner/scripts/skill-cleaner.ts --months 3

# ログなし・深掘りなし
node --experimental-strip-types skills/skill-cleaner/scripts/skill-cleaner.ts --no-logs

# カスタムトークン予算指定
node --experimental-strip-types skills/skill-cleaner/scripts/skill-cleaner.ts --context-tokens 272000 --budget-percent 2 --no-logs

# 任意のスキルルートを指定
node --experimental-strip-types skills/skill-cleaner/scripts/skill-cleaner.ts --root ~/Dropbox/boxd/skills --no-logs
```

## 削除・編集ポリシー

- **提案ファースト**: 変更はユーザーが明示的に求めるまで実行しない
- **削除候補の優先度**: Codexシステムスキル > プラグインスキル > 個人/リポジトリコピー（上位が残す対象）
- **保護すべきスキル**: リポジトリポリシーや運用手順をエンコードしたOpenClawメンテナー向けスキルは削除しない
- **説明文のトリガー名詞を保持**: 製品名・ツール名・アクション名・対象名はバジェット削減でも除去しない

## 関連

- [[concepts/self-refining-skills]] — LLMスキルに自己改善ループを組み込む設計パターン（LESSONS.mdアプローチ）
- [[concepts/claude-skills]] — Claudeスキルの概念（永続的職務定義ファイル・Claude.ai/Claude Code両対応）
- [[tools/obsidian-skills]] — kepano製 Obsidian向けAgent Skills集（agentskills.io仕様準拠）
- [[tools/gstack]] — Garry Tan製AIソフトウェアファクトリー（Claude Code拡張・23スキル収録）
- [[tools/codexbar]] — 同じ steipete 製。Codex/Claude Code の使用制限残量をメニューバーに表示するアプリ
- [[concepts/instruction-patch-lifecycle]] — 本ツールが検出する「スキル予算超過」がClaude Code側でどう現れるか（説明文の合計上限15,000文字を超えるとエラーなしで一覧から落ちる、と@kimuai08が報告）と、スキルを定期的に全部外して戻す運用の側
