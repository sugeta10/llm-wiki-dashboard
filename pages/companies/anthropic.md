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

## コミュニティアンバサダープログラム（2026-06）

@ClaudeDevs 公式発表（2026-06-22）。世界各地でClaude活用コミュニティを運営する **Claude Community Ambassador** プログラムの日本版が始まった。北海道から沖縄まで全国で応募を受け付ける。

- 既存アンバサダーは **37カ国・107都市で290回以上のミートアップ** を主催し、累計 **40,000人以上** が参加してきた実績がある
- 応募窓口: claude.com/ja/community/ambassadors

ユーザーコミュニティ主導でClaude活用知見を地域に広げる施策であり、製品販売・エンタープライズ展開とは別軸の草の根採用チャネルにあたる。

応募窓口の公式ページ（claude.com/ja/community/ambassadors）が公開済みで、Anthropic自身が「Claude で開発する仲間と出会い、共に未来を考え続けよう」と募集メッセージを掲げている。これによりプログラムは@ClaudeDevsのツイート発表に加えて公式サイト上でも確認できる一次情報になった。

## 関連

- [[tools/claude-code]]
- [[tools/claude-mcp]]
- [[concepts/agentic-coding]]
- [[concepts/prompt-engineering]]
- [[companies/openai]]（競合・同時期に垂直統合戦略を展開）
- [[business/ai-vertical-integration]]（AI企業垂直統合の産業分析）
- [[concepts/recursive-self-improvement]]（Anthropic Institute論考：AIがAI開発を加速し後継機建造へ向かう内部証拠）
- [[models/claude-fable-5]]（初の一般公開Mythosクラスモデル Fable 5 / Mythos 5・2026-06-10）
- [[models/claude-fable-5-1]]（Fable 5.1 / Mythos 5.1・2026-09-01発表。詳細仕様は未収集）
- [[companies/apple]]（WWDC26: XcodeのコードAI生成でClaudeが選択可能）
- [[tools/claude-tag]]（SlackにClaudeをチームメイトとして常駐させる製品・2026-06-23発表）
- [[business/founders-playbook]]（AIネイティブ・スタートアップ構築の公式プレイブック・Chat/Cowork/Code製品マトリクス）
- [[concepts/product-role-archetypes]]（Boris Cherny が Anthropic 内で観察した職能横断の5アーキタイプ）
- [[tools/claude-academy]]（公式の無料学習サイト・2026-08-20公開。製品別5トラック＋AI Fluency）
