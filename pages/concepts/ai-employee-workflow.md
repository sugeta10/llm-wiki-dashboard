# AI Employee Workflow

Claudeを完全自律動作する「AIスタッフ」として機能させるための**7日間セットアップ手法**。著者の@eng_khairallah1は、AIスタッフを「使う人」と「管理する人」を分けるのはスキルでも課金プランでもなくセットアップの有無だと主張し、1日1テーマで次を積み上げる。

- **Day 1（役割定義）**: 対象領域を1つに絞り、完璧な業務デイ・自律判断の範囲・エスカレーション条件・品質基準を1ページで文書化する。これがシステムプロンプトになる。
- **Day 2（インターフェース選択）**: 非技術者は Claude Cowork（自律ファイル操作・スケジュールタスク）、開発者は Claude Code（ターミナル・API・MCP連携）を著者は推奨する。
- **Day 3（ワークフロー設計）**: Trigger / Inputs / Process / Output の4要素でワークフローを定義し、まず1本だけ動かす。
- **Day 4（メモリ・コンテキスト）**: Claude Cowork はセッション横断メモリ、Claude Code は CLAUDE.md で永続コンテキストを管理。事業概要・品質基準・過去事例・ツール一覧・ルールの5要素を文書化する。
- **Day 5（ツール接続）**: Gmail / GCal / GDrive / Slack / Notion / M365 / GitHub / Linear コネクタで行動範囲を広げる。著者は各コネクタが能力を「乗算」すると表現する。
- **Day 6（ルーティンスタック）**: 日次・週次・イベントトリガー・オンデマンドの4ワークフローを揃える。
- **Day 7（改善ループ）**: 毎週金曜に全ワークフローのアウトプット品質を1〜10点で評価し、弱い出力の原因を診断してプロンプトを更新する。

**5つのAIスタッフ原型**: Content Engine（コンテンツ研究・下書き）、Operations Manager（メール／ファイル／請求処理）、Code Reviewer（PR／バグ／ドキュメント）、Research Analyst（競合／市場／ニュース）、Customer Support Agent（チケットトリアージ／下書き）。著者は、Managed Agents の Dreaming 機能を使えば、AIスタッフ自身が自分のアウトプットを評価し改善するメタワークフローをセッション間で自動実行できるとする（[[concepts/managed-agents-dreams]]）。

関連: [[concepts/managed-agents-dreams]] / [[tools/claude-managed-agents]] / [[concepts/claude-best-practices]] / [[concepts/multi-agent-patterns]]

## 観察ログ（未検証）

- 2026-05-12: 日次・週次・イベント・オンデマンドの4ワークフローを揃えると週4〜10時間の節約になると著者は試算（単一ソースの効果見積もり）

## 検証済み事実

- 2026-05-12: Claude Proは月$20、Claude Maxは月$100〜$200（Anthropic公式価格）
