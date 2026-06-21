# Hermes Agent

> **TL;DR**: Nous ResearchのオープンソースAIエージェント基盤で、自己進化スキル・3層メモリ・GEPAオフライン最適化を1フレームワークに統合し「使うほど賢くなる」設計を実現する。

既存のエージェントはセッションをまたいで賢くならない。メモリは容量と即応性のトレードオフに突き当たり、エージェントが自作したスキルは整理されず溜まり続け、しかも当人は自分のタスク結果を過大評価しがちで自己改善の方向を誤る。Hermes Agentはこの3つを、3層メモリ・Curatorによるスキルのガベージコレクション・GEPAによる外部からのオフライン最適化という形でそれぞれ構造的に手当てする。

## アーキテクチャ

メモリは3層に分かれ、容量と即応性をトレードオフする：Tier 1（常時コンテキスト内・容量小）→ Tier 2（SQLite検索・容量無制限）→ Tier 3（外部プロバイダー・プリフェッチ）。

| コンポーネント | 役割 |
|---|---|
| **SOUL.md** | アイデンティティ層。システムプロンプト先頭に挿入。手書き・静的 |
| **MEMORY.md**（2,200字上限） | 環境・プロジェクト慣習・教訓。セッション開始時に注入（Tier 1メモリ） |
| **USER.md**（1,375字上限） | ユーザープロファイル（名前・通信スタイル等）（Tier 1メモリ） |
| **SQLite (FTS5)** | 全セッション履歴をフルテキスト検索可能な形で保存（Tier 2メモリ） |
| **外部メモリプロバイダー** | 8種のプラグイン（Tier 3メモリ）。同時有効化は1つのみ |
| **スキル** | エージェントが自作するMarkdown手順書。Curatorがガベージコレクション |
| **GEPA** | 実行トレースを解析し進化的探索でスキルをオフライン最適化するパイプライン |

## 自己進化ループ

スキルはエージェントが自分で書き溜める永続的なMarkdown手順書として扱われ、生成・整理・最適化の3段で循環する。

```
1. エージェントが複雑タスクを試行錯誤で解決（5+ツール呼び出し等でトリガー）
2. skill_manageツールでSKILL.mdを自作保存
3. Curatorが定期的に統合・整理（archive/consolidate）
4. GEPAがオフラインで実行トレースを分析 → 改善案をPR形式で提案（直接コミットしない）
```

**Curator**はスキルのガベージコレクタで、アイドル時にバックグラウンド起動し、長期間未使用のスキルをstale→archiveへと段階的に整理する。自動削除はせず、archiveは1コマンドで復元できる。

**GEPA（Genetic-Pareto Prompt Evolution）**は実行トレースを進化的探索で解析し、スキルをオフラインで最適化するパイプライン。GPUは不要でAPI呼び出しのみで動作し、改善案を直接コミットせずPR形式で提案する。エージェントが自分のタスク結果を過大評価しがちな「自己評価バイアス」を、外部からの最適化によって補う設計になっている。

## 設定モード・プロファイル

**マルチエージェントプロファイル**で、プログラマー・デザイナー・リサーチャーなど役割ごとに設定・メモリ・スキル・SOUL.mdを完全分離できる。これは[[concepts/multi-agent-patterns]]の実装例にあたる。

「スタッフエンジニア」モードでは[[tools/claude-code]] CLIを下位エグゼキューターとして使い、Claude Maxサブスクをベースに動作させられる。

## 検証済み事実

- 2026-05-13: GEPA論文はICRL 2026 Oral、NousResearch/hermes-agent-self-evolution リポジトリで公開（MITライセンス）
- 2026-05-13: 公式Skills Hubに687スキル（built-in 87 + optional 79 + Anthropic製16 + LobeHub製505）
- 2026-05-13: エージェントループの上限は90ターン。サブエージェントもこのバジェットを共有
- 2026-06-21（公式README）: 端末バックエンドは6種——local / Docker / SSH / Singularity / Modal / Daytona。Daytona と Modal はサーバーレス永続化に対応し、アイドル時は環境がハイバネートして使用時のみ課金される
- 2026-06-21（公式README）: メッセージング窓口は Telegram / Discord / Slack / WhatsApp / Signal / Email / CLI を単一ゲートウェイプロセスで統合する。音声メモの文字起こしとプラットフォーム横断の会話継続に対応
- 2026-06-21（公式README）: OpenClaw からの移行コマンド `hermes claw migrate` を提供。SOUL.md・MEMORY.md/USER.md・ユーザー作成スキル・コマンド許可リスト・メッセージング設定・APIキーをインポートする
- 2026-06-21（公式README）: 外部ユーザーモデリングに Honcho（plastic-labs/honcho）の dialectic user modeling を採用。スキルは agentskills.io オープン標準に準拠
- 2026-06-21（公式README）: Nous Portal は単一サブスクで300+モデルと Tool Gateway をまとめる。Tool Gateway は web検索（Firecrawl）・画像生成（FAL）・TTS（OpenAI）・クラウドブラウザ（Browser Use）を束ね、ツール単位でのBYOキー併用も可能
- 2026-06-21（公式README）: ネイティブWindows対応。WSL不要で MinGit を同梱し、システムのGitに触れず独立動作する

## 観察ログ（未検証）

- 2026-05-13: @akshay_pachaar によるマスタークラス記事。Hermes Agentは「メモリ+スキル+学習ループ」を1フレームワークで実現する唯一のオープンソースエージェントと主張
- 2026-05-13: [[tools/openclaw]]との比較：「HermesはメッセージングGWの周りに学習エージェントをパッケージ、OpenClawはエージェントの周りにメッセージングGWをパッケージ」（Kiloブログの表現）
- 2026-05-13: GEPAの1回の実行コストは約$2〜10との記載（単一ソース・未確認）
- 2026-05-13: Curatorは7日間非稼働+2時間アイドル時に起動、30日未使用でstale、90日でarchiveという具体的な閾値（単一ソース）
- 2026-05-13: 2ヶ月でGitHubスター9万超えとの主張（未確認）
- 2026-05-21: X Premium（旧Twitter Blue）サブスクリプション対応に伴い再びトレンドに。X上でHermes Agentの理論解説記事が話題になっている（@shota7180 の日本語解説含む）
- 2026-06-03: @IBuzovskyi による10ハック解説記事。macOS/Windows/Linux デスクトップアプリの新提供・`localhost:9119` ダッシュボード・Kanban（SQLite・7ステータス・60秒ディスパッチ）の詳細が含まれる。「チャットアプリとして使うと90%の機能を残す」と主張（X bookmark: 2,421）

## 実践的な運用パターン（10ハック）

Hermesを「チャットアプリ」として使うと機能の90%を残すことになる。以下10のセットアップを積み重ねると、24時間稼働する自動化システムに変わる。

| セットアップ | 概要 |
|---|---|
| **Mission Control** | `localhost:9119` のダッシュボードで全タスク・スキル・Kanbanを俯瞰（macOS/Windows/Linux デスクトップ版も提供） |
| **Event Triggers** | Notionのカード移動・Sheetsの更新など「状態変化」を検知して自動反応。cronポーリング（10分間隔）またはWebhookで即時反応 |
| **Cron Jobs** | 自然言語で「毎朝〇時に〜して」と記述するだけで定期実行。crontab記法は不要 |
| **/goal 構造化** | Outcome（達成条件）/Sources（参照先）/Constraints（制約）/Deliverable（完了物）の4フィールドで目標を構造化するとファジーな結果を排除できる |
| **Sub-agents** | 研究タスクを複数のサブエージェントに並列割当。各エージェントが独立したコンテキストウィンドウを持ち、メインセッションは要約だけを受け取る |
| **Telegram Topics** | Telegramのトピック機能でYouTube・コーディング・リサーチ等ワークスペースを分離し、コンテキスト混濁を防ぐ |
| **Kanban Board** | SQLite永続ストレージ。Triage→To-Do→Ready→Running→Blocked→Done の7ステータス。60秒ごとに自動ディスパッチ。ゾンビ検知＋ハートビート監視付き |
| **Skills as SOPs** | 繰り返す作業を一度スキル化すれば次回以降は自動実行。エージェントが複雑タスク完了後にスキルを自作保存（`~/.hermes/skills/`） |
| **Webhooks** | 新リード到着・GitHubのPRオープン・競合コンテンツ投稿などの外部イベントをHermesゲートウェイで受信→即時対応（Make/Zapier/n8n対応） |
| **プロファイル分離** | 役割ごとにモデル・ツール・権限・メモリを完全分離。プロファイルはgitで他者に配布可能 |

これら10設定は組み合わさると複合的な効果を発揮する：コンテンツトレンド監視→ドラフト生成→Kanban記録→承認待ち、という1本のチェーンが朝の数時間に自動完結する。

## 問い

- 3層メモリの自動振り分けは実運用でコンテキスト圧迫を防げるか、それとも手動チューニングが必要か
- GEPAの「自己評価バイアスを外部補完する」設計は、自分のwikiシステムの自己改善ループにも応用できるか
- Curatorのstale/archive閾値（30日/90日）は妥当か、自分の利用頻度なら何日が適切か

## 関連

- [[tools/openclaw]] — 直接競合。アーキテクチャ哲学が真逆
- [[concepts/self-refining-skills]] — Hermesのスキル自己改善は同コンセプトを大規模に実装
- [[concepts/eval-loop]] — Hermesのスキル・メモリ・cron・承認ボタンを使ったAI出力品質ゲートの構築例
- [[tools/dspy]] — GEPAはDSPyのCOPRO/GEPA最適化器と関連する概念
- [[concepts/multi-agent-patterns]] — Hermesのプロファイル機能はマルチエージェント設計パターンの実装例
- [[tools/claude-code]] — プログラマープロファイルでのサブエグゼキューターとして連携可能
- [[concepts/claude-skills]] — Claude Skillsと類似した「永続スキル」の概念
- [[tools/hermes-agent-overnight]] — 夜間9時間自動化ワークフローの詳細設計（SOUL.md制限・5層セキュリティ・段階的セットアップ）
- [[tools/hermes-agent-self-evolution]] — GEPAによるスキル/プロンプト/コードのオフライン進化を担う独立リポ（5フェーズ計画・ガードレール5項目）
- [[tools/hermes-agent-research-department]] — プロファイル分離を使った3エージェント（Scout/Analyst/Briefer）リサーチ部門の具体的構築ガイド
