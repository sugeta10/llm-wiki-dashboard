# Google Agent Skills

Googleが公開するAIコーディングエージェント向けの**Agent Skills**集（GitHub: google/skills）。[[tools/claude-code]]・OpenAI Codex CLIなど[agentskills.io](https://agentskills.io)仕様に準拠したエージェントに対して、Google Cloud製品のドキュメントとベストプラクティスをスキルとして提供する。

## インストール

```bash
npx skills add google/skills
```

インストール時に必要なスキルのみ選択して追加できる。

## 利用可能なスキル（2026-05時点）

| スキル | 内容 |
|-------|------|
| Gemini API in Agent Platform | Gemini API統合・エージェントプラットフォーム |
| AlloyDB Basics | AlloyDB（PostgreSQL互換マネージドDB）の基礎 |
| BigQuery Basics | 大規模データ分析サービス |
| Cloud Run Basics | サーバーレスコンテナ実行環境 |
| Cloud SQL Basics | マネージドRDBMS |
| Firebase Basics | モバイル/Webアプリバックエンド |
| GKE Basics | Kubernetes Engine |
| Recipe: GCP Onboarding | Google Cloudのオンボーディング手順 |
| Recipe: GCP Auth | GCP認証設定 |
| Recipe: Network Observability | ネットワーク可観測性設定 |
| WAF: Security | Well-Architected Framework セキュリティ設計 |
| WAF: Reliability | Well-Architected Framework 信頼性設計 |
| WAF: Cost Optimization | Well-Architected Framework コスト最適化 |

## 関連

- [[tools/obsidian-skills]] — kepano製Obsidian向けAgent Skills（同じagentskills.io仕様・参考実装）
- [[tools/claude-code]] — Claude Code（Agent Skillsの主要インストール先）
- [[concepts/claude-code-google-cloud]] — これらGCPスキルをE2E開発デモで活用するパターン
- [[tools/google-agents-cli]] — 同じ「コーディングエージェントへスキルを注入する」系譜のGoogle製CLI。ADKエージェントの足場〜デプロイを7スキルで一本化
