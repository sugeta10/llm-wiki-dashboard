# Self-Refining Skills（自己改善スキルループ）

LLM スキル（プロンプトテンプレート）に **自己改善ループ** を組み込む設計パターン。スキル実行のたびに学習記録を蓄積し、次回実行前に参照することで自動的に品質が向上する。

## パターン実装

スキルの末尾に以下の4行を追加する：

```
after every invocation, append a 2-line entry to LESSONS.md
field 1: input pattern that triggered this skill
field 2: what worked or what failed and the fix
before next run, read the last 10
```

## 仕組み

1. スキル実行後に `LESSONS.md` へ2行追記（入力パターン + 成否と対処）
2. 次回実行前に直近10件の記録を読み込む
3. 蓄積された事例から判断精度が徐々に向上

## 効果

- **ハルシネーション対策**: 過去の失敗パターンを記憶し同じ誤りを繰り返さない
- **文脈適応**: プロジェクト固有の慣習・命名規則を自動学習
- **知識の持続**: モデルのコンテキストウィンドウ外にも学習履歴を保持

## 関連

- [[papers/2026-peng-llm-memory-faulty]] — 継続更新によるLLMメモリ劣化の実証研究（このパターンの設計的リスクを示す）
- [[concepts/prompt-engineering]]
- [[tools/claude-code]]
- [[concepts/llm-wiki]]
- [[tools/hermes-agent]] — Hermesのスキル自己改善（Curator + GEPA）は同コンセプトを大規模に実装した事例
- [[concepts/claude-skills]] — Claude Skills（永続的職務定義ファイル）の概念
- [[design/ai-skills-design]] — AIエージェントのスキル概念とデザイン職のスキルが交差する現状考察
- [[concepts/skill-self-improving-loop]] — 本パターンを別セッション・別スキルに外部化した3段ループ（会話履歴→Issue→Routines→triage→PR）
- [[concepts/agent-reflection-layer]] — decisions-log＋週次パターン昇格でエージェントに省察層を持たせる設計。本パターンをメタ認知の枠組みで一般化したもの
- [[concepts/agent-skill-eight-layer-design]] — 「成功した会話からSkillを抽出する」プロンプトはGotcha蓄積を事後ログでなく初回設計時に一括抽出する変種
- [[concepts/improver-skill-pattern]] — Warpのinner/outerスキル型。フィードバック収集と改善提案を定期実行のimprover skillに外部化し、skill編集をPRで回す（Anthropic公式ブログ）
- [[concepts/instruction-patch-lifecycle]] — 育てる（LESSONS.md蓄積）だけでなく、モデル更新時に全部外して戻す「捨てる」工程を対にする運用論。蓄積した教訓にも期限があるという視点
- [[design/consulting-pptx-skill]] — レビュー指摘を規約ファイル（約80項目）へ1行ずつ追記し、作成前に毎回読ませ出力後に機械チェックする蓄積ループ。本パターンをスライド制作に当てた実例
