> **TL;DR**: 「各エンジニアリング作業が次の作業をより楽にする」複利的発想を、計画80%・実行20%のワークフローを9種のコアスキル＋多数のエージェントで実装したEveryInc製のClaude Code・Codex・Cursor対応プラグイン。

中核となる発想は**技術的負債の逆転**である。従来の開発では機能を追加するたびにコードベースが複雑化し後続作業のコストが増えるが、Compound Engineeringはレビューと**Compound Note（学習の文書化）**を各サイクルに組み込むことで、次のエージェントが同じ教訓を再学習せずに済む構造を作り、作業を重ねるほど後続が楽になることを狙う。重心は実行よりも計画側（計画80%・実行20%）に置かれる。インストールは `/plugin marketplace add EveryInc/compound-engineering-plugin`、Claude Code・Codex・Cursorに対応する。

コアループは `/ce-brainstorm`（要件のQ&A）→ `/ce-plan`（実装計画）→ `/ce-work`（ワークツリーで実行）→ `/ce-code-review`（マージ前のマルチエージェントレビュー）→ `/ce-compound`（学習の文書化）。上流に `/ce-strategy` があり、問題・アプローチ・ペルソナ・メトリクス・トラックを `STRATEGY.md` として記録、後続スキルがこれを参照することで戦略が各作業に流れ込む。`/ce-product-pulse` は実際のユーザー体験・エラー・パフォーマンスの時系列レポートを `docs/pulse-reports/` に保存し、過去のパルスが次のbrainstormとstrategyへのシグナルになる。

主要スキルの役割:

| スキル | 目的 |
|--------|------|
| `/ce-strategy` | STRATEGY.md作成・維持（問題・アプローチ・ペルソナ・指標） |
| `/ce-ideate` | 大局的アイデア生成・評価（brainstormの前段） |
| `/ce-brainstorm` | 要件ドキュメント作成のためのインタラクティブQ&A |
| `/ce-plan` | 要件から詳細実装計画へ変換 |
| `/ce-work` | ワークツリーとタスクトラッキングで計画を実行 |
| `/ce-debug` | 障害の再現→根本原因特定→修正→回帰テスト |
| `/ce-code-review` | マージ前のマルチエージェントコードレビュー |
| `/ce-compound` | 学習を文書化し将来作業を楽にする |
| `/ce-product-pulse` | 時系列ユーザー体験・パフォーマンスレポート |

`/ce-code-review` をはじめ複数スキルが [[concepts/multi-agent-patterns]] を活用する。目的の面では [[concepts/spec-driven-development]] と重なり、仕様→計画→実行→レビュー→知識化のループを自動的に積み上げていく設計といえる。

## 検証済み事実

- 2026-05-27: コアループは `/ce-brainstorm` → `/ce-plan` → `/ce-work` → `/ce-code-review` → `/ce-compound`（公式README）
- 2026-05-27: 上流スキル `/ce-strategy` がSTRATEGY.mdとして問題・アプローチ・ペルソナ・メトリクス・トラックを記録。後続スキルがこれを参照することで戦略が各作業に流れ込む（公式README）
- 2026-05-27: `/ce-product-pulse` で実際のユーザー体験・エラー・パフォーマンスの時系列レポートを `docs/pulse-reports/` に保存。過去パルスが次のbrainstormとstrategyにシグナルを提供（公式README）

## 観察ログ（未検証）

- 2026-05-27: 現在37スキル・51エージェント収録（単一ソースの数字、未確認）
- 2026-05-27: @nurijananianがSuperpowersから乗り換えたと推薦
- 2026-05-27: 設計思想「技術的負債の逆転」 — 従来開発は機能追加のたびに複雑化するが、Compound EngineeringはレビューとCompound Note（学習の文書化）により次のエージェントが同じ教訓を再学習しなくて済むという主張

## 問い

- 計画80%・実行20%の比率は自分のワークフローでも有効か、それともプロジェクト規模に依存するか
- Compound Note（`/ce-compound`）の学習蓄積は、自分のLLM Wikiの知識化ループと統合できるか
- [[tools/shaping-skills]]・[[tools/grill-me]]と機能が重複する部分はどれで、併用する意味はあるか

## 関連

- [[tools/claude-code]] — メイン対応プラットフォーム
- [[concepts/spec-driven-development]] — 仕様先行開発との思想的重複
- [[tools/shaping-skills]] — Shape Up由来のcomplementary計画スキル群
- [[tools/grill-me]] — 類似の「実装前深掘り対話」スキル
- [[concepts/multi-agent-patterns]] — `/ce-code-review`等がマルチエージェントを活用
- [[concepts/agentic-engineering-workflow]] — このプラグインを中核に据える実践者の総合ワークフロー
