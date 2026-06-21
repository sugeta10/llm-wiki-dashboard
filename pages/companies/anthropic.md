# Anthropic

AIセーフティ研究・開発を主軸とする企業。Claude シリーズの開発元。

## 主要製品

- **Claude** — LLMシリーズ（Claude 3.5 Sonnet, Claude 3 Opus 等）
- **[[tools/claude-code]]** — AIコーディング CLI ツール
- **[[tools/claude-mcp]]** — Model Context Protocol（外部ツール連携仕様）

## マルチエージェントフレームワーク

Anthropicが公開したマルチエージェント構成では：
- 人間1人が指示役、AI複数体が業務を分担
- [[concepts/agentic-coding]] の実践的な実装例

## プロンプト教育

初心者向けプロンプト講座をインタラクティブ形式で無料公開。チェーンプロンプト・Few-shot・ハルシネーション回避などを学べる（[[concepts/prompt-engineering]] 参照）。

## 金融サービス向けソリューション

銀行・保険・資産運用・フィンテック向けに特化した製品群を展開：
- **SOC 2・FedRAMP 準拠**、出力はすべてソース追跡可能
- **データ連携**: LSEG、FactSet、S&P Global、Morningstar
- **Claude Cowork** — バックグラウンドエージェント（レポート・データ取得）
- **Claude for Microsoft 365** — Excel / PowerPoint / Word / Outlook 統合
- **Agent テンプレート** — ピッチブック、KYC スクリーニング、クレジットメモ等

→ [[tools/claude-finance-agents]] で10テンプレートの詳細を参照

## 開発速度の秘密

「Anthropicはなぜ異次元の速度で開発できるのか」（2026-05-01）で指摘された要因：
- **小さく精鋭なチーム**: 人数を絞り、1人当たりの裁量と責任を最大化
- **研究者が直接製品を作る**: リサーチと製品開発の距離がゼロ
- **Claude を使って Claude を開発**: 自社ツールで開発速度を自己加速するフライホイール構造（→ [[concepts/recursive-self-improvement]] に内部データの定量化あり：コードの80%超をClaudeが執筆）
- **安全性研究が製品差別化**: Constitutional AI 等の研究が製品信頼性に直結

## 企業向けAIサービス会社の設立

2026年5月、[[companies/openai]] の Deployment Company 設立と同時期に、Anthropic も Blackstone・Hellman & Friedman・Goldman Sachs と組んで、Claude を企業の中核業務に組み込む AI サービス会社を設立。Applied AI エンジニアをパートナー企業のエンジニアチームと協働させる体制。

→ 背景・産業インパクト分析は [[business/ai-vertical-integration]] を参照。

## 関連

- [[tools/claude-code]]
- [[tools/claude-mcp]]
- [[concepts/agentic-coding]]
- [[concepts/prompt-engineering]]
- [[companies/openai]]（競合・同時期に垂直統合戦略を展開）
- [[business/ai-vertical-integration]]（AI企業垂直統合の産業分析）
- [[concepts/recursive-self-improvement]]（Anthropic Institute論考：AIがAI開発を加速し後継機建造へ向かう内部証拠）
- [[models/claude-fable-5]]（初の一般公開Mythosクラスモデル Fable 5 / Mythos 5・2026-06-10）
- [[companies/apple]]（WWDC26: XcodeのコードAI生成でClaudeが選択可能）
