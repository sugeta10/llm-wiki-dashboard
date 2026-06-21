# PM Skills

Pawel Huryn が公開するプロダクトマネジメント向けの Claude Code スキル集（OSS・MIT・GitHub 12K stars）。プロダクト発見・検証・意思決定の方法論を「日々のワークフローに組み込めるスキル／コマンド」として配布し、PM の思考プロセスをエージェントに肩代わりさせる。[[concepts/claude-skills]] の PM ドメインへの具体적用例。

著名 PM 方法論の「型」をスキルとして内蔵しているのが特徴 —— Teresa Torres（継続的ディスカバリー／Opportunity Solution Tree）、Marty Cagan（プロダクト発見・Empowered チーム）、Alberto Savoia（Pretotyping＝作る前に確かめる）の rigor を、自分のワークフローに直接効かせられる。代表コマンドに `/red-team-prd`（PRD を敵対的にレビューして穴を炙り出す）がある。

これは「企業の最初のAI戦略はスキルライブラリ」（[[business/skill-library-strategy]]）の思想を PM 職能で体現したもの: アクセスではなく**判断の言語化**をスキルとして資産化し、誰でも再利用できる形に落とす。

## 検証済み事実

- 2026-06-05: 作者 Pawel Huryn が v2.0 を告知。**9 plugins / 68 skills / 42 commands**、Free・MIT ライセンス。OSS リポジトリは GitHub **12K stars**（作者の自己公表値）
- 2026-06-10 (公式リポジトリREADME): 9プラグインの内訳は pm-product-discovery（13skills/5cmd）・pm-product-strategy（12/5）・pm-execution（16/11）・pm-market-research（7/3）・pm-data-analytics（3/3）・pm-go-to-market（6/3）・pm-marketing-growth（5/2）・pm-toolkit（4/5）・pm-ai-shipping（2/5）。**Codex CLI はClaude Codeと同じmarketplaceファイルを直接読むためネイティブ導入可**（ただし`/slash`コマンドはClaude Code専用、Codexではプレーン言語で記述）。Gemini CLI/OpenCode/Cursor/Kiro は `SKILL.md` をコピーすればスキルのみ利用可
- コマンドはスキルを連鎖させるワークフロー。例: `/discover` = brainstorm-ideas → identify-assumptions → prioritize-assumptions → brainstorm-experiments の4スキル連鎖。各コマンド完了時に次のコマンドを提案しPMワークフローに沿って流れる設計
- companion として [[tools/pm-skills]] とは別に **PM Brain**（Pawel Huryn製、ラップトップ上のplain markdownフォルダをClaudeが回答前に読み・回答後に書き・毎週金曜にsweepする「PM向け第二の脳」。vector DB/クラウド/agent memory無し）が組み合わせ可能

## 観察ログ（未検証）

- 2026-06-05 (@PawelHuryn): Teresa Torres / Marty Cagan / Alberto Savoia の rigor が日々のワークフローに組み込まれている、という作者の主張。`/red-team-prd` をリンク付きで紹介
- 2026-06-06 (@PawelHuryn): 「agentic engineering の究極コンボ」として4コマンドの連鎖を提示——`/document-app`（システムドキュメントを生成）→ `/security-audit-static`（ドキュメント化した意図に対しセキュリティを静的検査）→ `/performance-audit-static`（over-fetching・index欠落・キャッシュを検査）→ `/derive-tests`（テスト済み/未テストの対応をマッピング）。ドキュメント＝意図の正本を起点に監査・テスト導出を連鎖させる設計思想（[[concepts/llm-doc-management]] と同型の「文書を契約にする」発想）

## 問い

- `/red-team-prd` のような敵対的レビューは、自前の [[tools/buy-or-bounce]]（バイヤーペルソナ並行シミュレーション）や [[tools/grill-me]]（要件掘り下げ）と何が違い、組み合わせる価値はあるか
- 既製の PM 方法論スキルをそのまま使うのと、自分のドメインで [[concepts/skill-building-best-practices]] に沿って書き起こすのと、どちらが続くか

## 関連

- [[concepts/claude-skills]] — Claudeスキルの概念（永続的職務定義ファイル）
- [[business/skill-library-strategy]] — 企業の最初のAI戦略はスキルライブラリ（判断の言語化が私有資産）
- [[concepts/skill-building-best-practices]] — スキル構築のベストプラクティス
- [[tools/buy-or-bounce]] — バイヤーペルソナ並行シミュレーションでLP/広告の障壁特定
- [[tools/grill-me]] — 実装前の徹底的要件掘り下げ対話スキル
