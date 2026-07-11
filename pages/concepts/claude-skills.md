# Claude Skills（Claudeスキル）

> **TL;DR**: タスク固有の「永続的な職務定義」をMarkdownファイルで与え、対応タスク時に自動ロードさせる仕組み。プロンプトと違いセッションをまたいで持続するため、毎回の文脈再構築が不要になる。

プロンプト中心の運用では、同じタスクに取り組むたびに「今回これをせよ」という指示を書き直す必要があり、セッションが終われば文脈は失われる。この毎回の文脈再構築こそがスキルの解こうとする課題で、解決の鍵は職務定義の「持続」と「自動ロード」という2つの性質にある（下表）。一度切り出した職務定義は再利用可能な能力として蓄積されていくため、「10倍の成果を出すビルダーは賢いのではなく、スキルを一度作って毎回再構築しないだけ」とも言われる。

| | プロンプト | スキル |
|---|---------|-------|
| **永続性** | セッション終了で消える | セッションをまたぐ |
| **ロード** | 毎回手動で書く | 対応タスク時に自動ロード |
| **役割定義** | 「今回これをせよ」 | 「このタスクでは常にこうあれ（職務定義）」 |

## 導入方法

Claude.ai と Claude Code は同じ仕組みでスキルを動作させる。

- **Claude.ai / Claude Desktop**: Settings → Sub-Agents → Add からインストールする
- **Claude Code**: プロジェクトルートの `.claude/agents/skillname.md` にMarkdownファイルを保存すると自動認識される

技術書からスキルを自動生成するには [[tools/book-to-skill]] が使える。PM向けの実践例として [[tools/wbs-skills]] も参照。

## 設計上の注意点

スキルの説明（description）は呼び出し対象を判定するために全コンテキストへロードされる。そのため冗長な説明を書くとトークンを無駄に消費し続ける。説明は簡潔に保ち、必要に応じてエージェントにトークン効率・文法省略を指示するのが望ましい。スキルの冗長箇所を検出する [skill-cleanerツール](https://github.com/steipete/agent-scripts/blob/main/skills/skill-cleaner/SKILL.md) のような補助ツールも存在する。

## スキルの実践カタログ（20スキル・5カテゴリ）

スキルが具体的にどんなタスクに適用できるかの例。タスクごとに独立した職務定義を切り出す設計が見て取れる。

**コンテンツ & ライティング**

| # | 名前 | 用途 |
|---|-----|------|
| 01 | Hook Forge | 10種類の心理的引き金（好奇心・喪失・対比・具体性・論争）に沿ったフック文10本生成 |
| 02 | Voice Locker | 文章サンプルからユーザーの声（voice）を永続ロック。以降の全成果物をその声で出力 |
| 03 | Thread Architect | X/Twitterスレッド構造設計（フック→テンション→payoff→CTA） |
| 04 | Repurpose Engine | ロング記事1本からスレッド・LinkedIn投稿・ニュースレター・フック3本・引用キャプションを一括生成 |
| 05 | Headline Lab | 15見出しをフォーマット別に生成・スコアリング・上位3件を提示 |

**調査 & 分析**

| # | 名前 | 用途 |
|---|-----|------|
| 06 | Brief Builder | 漠然としたリサーチ依頼を構造化調査ブリーフに変換 |
| 07 | Contradiction Finder | 複数ソース間の矛盾・対立点を抽出し議論の弱点を事前に発見 |
| 08 | Signal Scanner | 業界ニュースからシグナル（本質的な動向）とノイズを分離する週次ブリーフ |
| 09 | Assumption Auditor | 計画・議論に潜む「誰も疑わなかった前提」を洗い出す |
| 10 | Source Ranker | ソース一覧を信頼性・鮮度・関連性でランキング。弱い基盤で議論を組まないための前処理 |

**ビジネス & オペレーション**

| # | 名前 | 用途 |
|---|-----|------|
| 11 | SOP Writer | 箇条書きメモや音声記録からオーナー・ツール・ステップ付きのSOP（標準作業手順書）を生成 |
| 12 | Decision Framer | 行き詰まった意思決定を評価基準・選択肢・必要情報で構造化 |
| 13 | Meeting Extractor | 議事録から「決定事項・担当者・ブロッカー・暗黙の問題」の4点のみ抽出 |
| 14 | Pricing Stress Tester | 価格モデルを3方向（高すぎる客・品質疑念客・競合）でストレステスト |
| 15 | Offer Sharpener | 「自分が売ってると思うもの」vs「買い手が聞こえているもの」のギャップを検出 |

**コーディング & 開発**

| # | 名前 | 用途 |
|---|-----|------|
| 16 | Code Explainer | コードを「使う人向け」と「改修する人向け」の2レベルで説明 |
| 17 | PR Reviewer | バグ・テスト・セキュリティ・スタイル・アーキテクチャ議論候補の5点を抽出 |
| 18 | Debug Partner | エラーの根本原因診断。バンドエイド修正を却下・回帰テスト付き |

**戦略 & 思考**

| # | 名前 | 用途 |
|---|-----|------|
| 19 | Second Order Thinker | 意思決定の2次・3次の影響（効果の効果）を3手先までマッピング |
| 20 | Mental Model Applier | 問題に最適なメンタルモデルを3つ選択・適用して具体的洞察を生成 |

## 観察ログ（未検証）

- 2026-05-29: 佐藤亮（@nobita2040）著「**Claude Codeで学ぶ Agent Skills入門**」が2026-06-08発売予定。デザイン系・ドキュメント系・開発&コラボレーション系の3カテゴリでAgent Skillsを体系的に解説する書籍
- 2026-05-25: @steipete（Peter Steinberger）が指摘するアンチパターン：「スキル説明に本1冊分を書くな。全コンテキストに毎回ロードされるゴミになる」
- 2026-05-19: @sairahul1 による「プロンプトは期限付き、スキルは永続」という概念整理（20スキル・5カテゴリを紹介）
- 2026-05-19: 「10倍の成果を出すビルダーは賢いのではなく、スキルを一度作って毎回再構築しないだけ」という主張

## 問い

- 自分のwikiシステムの操作（ingest/lint/review）を、CLAUDE.md直書きではなくスキル化すると保守性は上がるか？
- スキル説明を簡潔に保ちつつ自動ロードの判定精度を保つ境界はどこか？description肥大の損益分岐点を測れるか
- カタログの20スキルのうち、どれが実際に再利用され続けるか（作って放置されないものの特徴）

## 関連

- [[concepts/skill-building-best-practices]] — スキルの「作り方」側：9カテゴリ分類・gotchas・description＝トリガー定義（Anthropic社内知見）
- [[concepts/self-refining-skills]] — スキルに自己改善ループを組み込む発展的なパターン（LESSONS.mdアプローチ）
- [[tools/grill-me]] — Matt Pocock作の実装前要件掘り下げ3行スキルの実践例
- [[tools/matt-pocock-skills]] — grill-me含む実践的エンジニアリングスキルコレクション
- [[tools/superpowers]] — 本概念の大規模な実装例（obra製14スキル＋毎セッション注入ブートストラップの開発方法論）
- [[tools/buy-or-bounce]] — コンバージョン改善のためのスキル活用例（5バイヤーペルソナ）
- [[tools/claude-code]] — Claude Code での `.claude/agents/` によるスキル運用
- [[tools/claude-code-plugins]] — Claude Code公式マーケットプレイスのスキル・エージェント一覧
- [[tools/hermes-agent]] — 類似コンセプトを実装。スキルを自己作成・進化させる
- [[tools/obsidian-skills]] — kepano製 Obsidian向けAgent Skills集（同仕様）
- [[concepts/cursor-instruction-methods]] — Skillsを他の指示手段（AGENTS.md/Rules/Commands/Subagents）と並べた使い分けフレーム
- [[business/skill-library-strategy]] — スキルを企業のAI戦略・私有資産として捉える論考（Hiten Shah）
- [[tools/ui-skills]] — UIエンジニア/デザインエンジニア特化のスキルカタログサイト（ui-skills.com）の実例
- [[concepts/agent-skill-management-system]] — スキルが増えた後の管理問題（発見・ライフサイクル・ガバナンス・合成・評価の5機能）
- [[tools/japanese-tech-writing]] — 日本語技術文書の文章規範に特化したSKILL.mdの実例（@golden_lucky）
- [[papers/2026-zhou-colleague-skill]] — 人物の作業痕跡からスキルを自動生成する研究。スキルの「梱包形式」を所与として、痕跡からの生成側を扱う
- [[papers/2026-li-skillsbench]] — Skillが実際どれだけ効くかを対試験で測るベンチマーク（curatedで平均+16.6pp、compact・少数精鋭が網羅的散文に勝つ）
- [[papers/2026-hao-skill-mining]] — GUI軌跡からSKILL.mdを自動採掘する診断研究。可読でも転移せず頻度ベースラインに負けるという負の結果
- [[tools/emil-kowalski-skills]] — デザインエンジニアの専門性（taste）をスキル化した実例（/apple-design・「AIは専門性を置き換えず増幅する」）
