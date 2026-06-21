# Claude Skills × MCP 高単価アフィリエイト自動化

> **TL;DR**: [[concepts/claude-skills|Claude Skills]] × [[tools/claude-mcp|MCP]] を組み合わせ、高単価アフィリエイトの案件選定・記事量産・SNS導線・cron自動運転を「設計」として一本化する10章・約40プロンプトのコピペ実践ガイド。

高単価アフィリで月100万円を目指す構造は「単価 × 成約数 × 自動運転」の3要素で分解できる。単価2〜10万円の案件を月10〜50件成約させる構造自体はシンプルだが、従来はそれを回す作業量が壁になっていた。Claudeは案件選定の数値化・ペルソナ設計・記事量産・SNS自動化・LP ABテストなど各工程に「コピペ可能なプロンプトを割り当てる相棒」として機能する。Claude Code × cronで最終的にほぼ全工程を自動運転に移行できる。

## 10章構成の概観

| 章 | テーマ | 主なプロンプト技術 |
|----|--------|-------------------|
| 1 | 案件選定 | Tier-S Niche Sniper / ASP Cross-Crawl / LTV Reverse Calculator |
| 2 | ペルソナ&オファー設計 | N=1 Persona Generator / Pain-Stack Mapping / Offer-Market Fit Test |
| 3 | 記事量産 | SERP Reverse Engineering / Skeleton-Then-Fill / E-E-A-T Injection |
| 4 | SNS導線 | Hook Library Builder / Thread-to-Article Bridge / Lead Magnet Generator |
| 5 | セールス文AB | Variant Spawner / Objection Handler / Story Arc Injector |
| 6 | プロンプト品質 | Output-First Specification / Negative Constraints / XML Structured Tagging / Self-Refine |
| 7 | MCP拡張 | Firecrawl / Supadata / Filesystem+Skills / Notion/Sheets |
| 8 | cron自動運転 | Daily Article Drafter / SNS Cannon / Weekly SERP Watcher / Monthly P&L Reporter |
| 9 | 落とし穴回避 | Compliance Guard / Approval-Rate Optimizer / ASP Diversification |
| 10 | 習慣を捨てる | 100記事主義の否定 / 単発自動化の否定 / 人間の最終チェック必須 |

## 設計思想のポイント

**案件選定が8割を決める**: 単価 × 承認率 × 検索需要の3軸をAIに採点させ、合計24点以上のジャンルのみを残す。低単価案件をどれだけ自動化しても、100件成約が必要なものを33件に圧縮できるのは選定段階だけ。

**[[concepts/claude-skills|Claude Skills]] + [[tools/claude-mcp|MCP]] の組み合わせ**: Filesystem MCPで`SKILL.md`に案件・ペルソナ・過去の検証結果を書き溜めると、セッションをまたいで文脈を引き継げる事実上の永続記憶になる。Firecrawl MCPで競合LPをMarkdown化してClaudeに直接分析させ、Notion/Sheets MCPで月次収益データを一元管理するのが第7章の構成。

**XML構造化タグ**: ClaudeはXMLタグの読解が特に得意なため、`<goal><context><constraints><examples><output_format>` のタグ構造で指示すると読解精度と出力品質が安定する（第6章）。

**自動運転と人間の最終ゲート**: 第8章の5つのcron設定（記事生成・SNS投稿・順位監視・月次レポート・不調記事検知）が揃うと、起床時にドラフトが机に置かれた状態になる。ただし公開前の目視チェックだけは省かないことが長期運用の要件として強調されている。

## 観察ログ（未検証）

- 2026-06-11: @manerun_ は「月100万は誰でも数ヶ月で行ける魔法ではない」と断った上で、構造のシンプルさを強調。約40プロンプトはすべてコピペ前提で記述されており、「[  ]の部分だけ自分の案件名・ジャンル名に書き換えて使う」設計
- 2026-06-11: 第9章のCompliance Guard（薬機法・景表法・特定商取引法の3観点でリスク表現を抽出）は、高単価ジャンル（美容・健康・金融・転職）で特に重要とされる。AIによる事前チェックが行政指導リスクの低減に効くとの主張

## 問い

- cron × Claude Codeのパターンは launchd + `claude -p` を使うこのwikiの自動ingestと同じアーキテクチャか。共通点と差分を整理する価値がある
- Filesystem MCPの永続記憶は[[concepts/claude-skills|Claude Skills]]（SKILL.md）と実質的に同じ仕組みか、それとも読み込みタイミングや権限モデルに差があるか
- Compliance Guardプロンプトは法的判断として一次確認を代替できるか（AI出力を最終根拠にするリスクの検討が必要）

## 関連

- [[concepts/claude-skills]] — Claudeスキルの概念（永続的職務定義ファイル・再利用可能指示書）
- [[tools/claude-mcp]] — Model Context Protocol（Firecrawl/Filesystem等の外部連携）
- [[business/ai-income-retirement-strategy]] — AIを使った自動収益化と早期退職の戦略
- [[tools/claude-code]] — Claude Code（cron連携での自動運転に使うCLIツール）
