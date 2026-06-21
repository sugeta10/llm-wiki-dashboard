# AI Engineer ロードマップ（2026）

> **TL;DR**: 2026年のAIエンジニアの仕事は「モデルを選ぶ・プロンプトを書く」ことではなく、モデルを取り囲む**ハーネス**（エージェントループ・コンテキスト管理・eval・耐久性）を設計し運用すること。同一モデルでもハーネス次第でベンチマークが10〜36ポイント動く。

「賢いラッパー」と「本番で生き残るシステム」の差がそのまま市場価値の差になっている。GPTを数個のプロンプトで包んだだけのものは機能であって事業ではなく、Big Techにいつでも飲み込まれる（Sherlocked）。企業が金を払うのは、深夜に壊れないエージェント・回帰を計測で証明できるシステム・同じモデルを大幅に性能向上させるハーネスの方。**ハーネスこそが仕事の本体**であり、プロンプトエンジニアリングは単独スキルとしては終わり、[[concepts/12-factor-agents]] に通じる**コンテキストエンジニアリング**がそれを置き換えた。

## コンテキストエンジニアリングの4プリミティブ

エージェントエンジニアが扱う「モデルの前に何のトークンを置くか」の操作は4種類に集約される。これは [[tools/claude-harness]] / [[concepts/claude-code-large-codebase]] のハーネス設計と地続き。

- **Write** — スクラッチパッド・メモリファイル（エージェントが読み書きする外部記憶）
- **Select** — 必要な時点での検索（前倒しの一括投入でなく、使う地点でのretrieval）
- **Compress** — コンテキストウィンドウの85〜95%地点での要約・圧縮（[[concepts/prompt-caching]] と併用）
- **Isolate** — サブエージェントに独立コンテキストを持たせる（[[tools/claude-code-subagents]] / [[concepts/multi-agent-patterns]]）

## Workflow と Agent の使い分け

- **Workflow**: 制御フローを人間が固定で書く（[[concepts/multi-agent-patterns]] のAnthropic5パターン: Prompt chaining / Routing / Parallelization / Orchestrator-worker / Evaluator-optimizer）
- **Agent**: ループ内でモデル自身が制御フローを決める
- ワークフローで足りる場面でエージェントを作ると、コストは約10倍・故障率は2倍になる。最初に正しく見極める。

## ハーネス＝OS というメンタルモデル

モデル=CPU（生の計算）、コンテキストウィンドウ=RAM、ハーネス=OS、スキル=アプリ。OSがCPUの実力を決めるのと同じで、ハーネスがモデルの実力を決める。モダンなハーネスの構成要素は概ね10個に分解できる：ループ制御 / ツールディスパッチ（レジストリ・スキーマ検証・並列・リトライ）/ コンテキスト管理（85%地点でcompaction）/ 永続化（各ノードでcheckpoint、resume/rewind/fork可能に）/ サブエージェントオーケストレーション / スキル＋progressive disclosure（[[concepts/skill-building-best-practices]]）/ フック（Pre/PostToolUse・PreCompact・Stop）/ 観測性（モデル・ツール・サブエージェント呼び出しごとのOTELスパン）/ サンドボックス / 認証ブローカリング（モデルにクレデンシャルを見せない）。

## evalがボトルネック

「改善した」をvibesでなく計測で言えるエンジニアが希少。実装すべきeval4種：

1. **Single-turn eval** — 入力に対し出力が正しいか。最安・決定論的グレーダー優先・常時実行
2. **Trajectory eval** — 正しいツールを正しい引数・正しい順序で呼んだか（single-step / full-turn / multi-turn）
3. **LLM-as-judge** — 自由記述出力（レポート・コードレビュー）向け。人手採点と週次でキャリブレーション（[[concepts/eval-loop]]）
4. **End-state eval** — ステートフルエージェント向け。DB・ファイルの最終状態をground truthと比較

注意点: モデルは「評価されている」ことを検知して挙動を変える。合成クエリでなく実本番クエリで組む。

## 本番ハードニング（終わらないフェーズ）

- **コスト規律**: CLAUDE.md・システムプロンプト・ツール定義をキャッシュ（[[concepts/prompt-caching]]・最大90%削減）/ 難度でルーティング（Haiku→Sonnet→Opus）/ 非リアルタイムはBatch API（50%オフ）/ マルチエージェントは単一の約15倍トークンを焼くので価値が見合う時だけ（[[concepts/token-management]]）
- **レイテンシ**: 並列ツール呼び出し常用 / 部分出力ストリーミング / サブエージェントfan-out
- **安全性**: コード実行は必ずサンドボックス（Modal/E2B）/ クレデンシャルはモデルコンテキスト外でブローカー / 不可逆操作はhuman-in-the-loop
- **モニタリング**: トークンコスト・ツール失敗率・judgeスコア・p95でアラート / モデルアップグレード後はeval再ベースライン（ハーネスはモデルの「できないこと」を前提に書かれており、その前提が陳腐化する）
- **レジリエンス**: 60秒超のエージェントは耐久実行（Inngest/Temporal/PostgresSaver）/ 各ノードでcheckpoint

## スキップしてよいこと

- **モデル学習・ファインチューニング**: API で十分なケースが大半。コストと時間が見合わない
- **独自ベクトルDB構築**: 実際のrecall問題を計測する前に作らない。既存サービスで足りる
- **No-code エージェントプラットフォーム**: 使い捨て用途以外は不要
- **OpenAI Swarm / Assistants API**: 前者は公式に本番非対応、後者は2026年半ばにsunsetting

## 観察ログ（未検証）

「How To Become An AI Engineer in 2026」（@sairahul1, 2026-06-05, X bookmark 2,378）の固有主張・数字：

- 2026-06-05: 同一モデル（Opus 4.5）をハーネス違いでCOREベンチに通すと、Claude Codeハーネス78% vs Smolagentsハーネス42%（36ポイント差）と主張。「賢いラッパー」と「本番システム」の給与差は約$150,000とも
- 2026-06-05: 6フェーズ・17週間（フルタイム）/ 40週間（副業）ロードマップを提示。Phase 0=メンタルモデル / Phase 1=生SDK＋Claude Agent SDKで素のエージェント二度書き / Phase 2=LangGraph+Deep Agentsで本番構成 / Phase 3=自前ミニハーネス約1,500行 / Phase 4=eval・回帰ハーネス（golden 30-50問・CI gate） / Phase 5=本番ハードニング（永久）
- 2026-06-05: 推奨スタックは LangGraph 1.0 + Deep Agents（state machine＋PostgresSaver耐久性＋time-travel debug＋OTEL）、ハーネス参照実装は Claude Agent SDK（Claude Codeと同一ハーネス）、観測性は LangSmith / Braintrust / Arize Phoenix から1つ
- 2026-06-05: CrewAI=デモ最速だが本番脆弱、AutoGen=Microsoft Agent Frameworkへ統合で先行き不透明、との評
- 2026-06-05: ベンチ数字（2026年5月時点・単一ソース）: SWE-bench Verified Claude Opus 4.7 ~87.6% / GPT-5.5 ~88.7%、GAIA Claude Sonnet 4.5 74.6%、τ-bench Claude Mythos Preview 89.2%
- 2026-06-05: 「57%のチームが本番にエージェントを持ち、そのうち89%が観測性を配線済み」と主張

「What to Learn, Build, and Skip in AI Agents (2026)」（@rohit4verse）の整理：

- エージェントは「賢い自動化」でなく「自律的な判断主体」として設計する時代
- ハーネス設計（[[tools/claude-harness]]）が差別化要因
- 学ぶべき基礎: LLM API活用 / [[concepts/prompt-engineering]] / [[concepts/agentic-coding]] / RAG / eval
- 作るべきもの: 専用エージェント / 社内 [[concepts/llm-wiki]] / CI組込み自動コードレビュー / データパイプライン

## 問い

- 「同一モデル×ハーネス違いで36ポイント差」は自分のwikiシステムのハーネス（[[tools/claude-harness]]）でも再現するか？小さなeval setで測れるか
- Phase 3「自前ミニハーネス1,500行」を実際に書くと、Claude Agent SDK / Deep Agents に対し何を切り何を得るか
- このロードマップの「ハーネスが仕事の本体」という主張は、[[concepts/ai-era-good-engineer]]・[[concepts/agentic-engineering-workflow]] の実践者像とどこまで一致するか

## 関連

- [[concepts/harness-engineering]] — ハーネスの概念定義・5アーティファクト・3陣営・ハーネス崩壊（Build to Delete）の全体論
- [[tools/claude-harness]] — ハーネス＝仕事の本体という本ページの中心主張の実体
- [[concepts/12-factor-agents]] — コンテキストエンジニアリング・ステートレス設計の原則
- [[concepts/multi-agent-patterns]] — Anthropic5ワークフローパターン・サブエージェント設計
- [[concepts/eval-loop]] — 生成→採点→ゲートの品質ループ（eval4種の基盤）
- [[concepts/agentic-engineering-workflow]] — plan先行・大量並列の実践者総合スタック
- [[concepts/skill-building-best-practices]] — スキル＋progressive disclosure
- [[concepts/agentic-coding]]
- [[concepts/prompt-engineering]]
- [[concepts/llm-wiki]]
- [[tools/claude-code]]
