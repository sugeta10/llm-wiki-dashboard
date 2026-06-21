# Claude Code on Google Cloud（E2E開発フロー）

> **TL;DR**: Claude Code＋Vertex AI上のClaudeモデルで、PM/UX/SWE/Security/Dataの5ペルソナを1人が演じ、フィードバックアプリをゼロからGCPへデプロイするまでをライブ実演したパターン。スキル・MCPサーバー・サブエージェントがSDLCの各工程を埋める。

エンタープライズのソフトウェア開発ライフサイクル（SDLC）は本来 PM→UI/UXデザイナー→ソフトウェアエンジニア→セキュリティエンジニア→データ担当（グロースマーケ/アナリスト）という複数ロールの連携で回る。このパターンの主張は、Claude Code の3つの構成要素（**Skills / MCPサーバー / サブエージェント**）が各ロールを増強し、1人がすべての帽子をかぶってE2Eで完結できる、というもの。モデルを Google Cloud（Vertex AI）上で動かすことで、エンタープライズ要件（課金・データ所在・可用性）を満たしながら同じ体験を得る。

## なぜ Claude on Google Cloud か

- **従量課金**：メッセージ上限でなくトークン単位。本番向けには Provisioned Throughput でスループットを予約できる
- **セットアップが容易**：Application Default Credentials（ADC）が認証情報を自動検出。**ローテーションすべきAPIキーや環境変数が不要**（[[concepts/workload-identity-federation]] と同系統の発想）。Claude Code に project/region 検出とモデルpin留めのウィザードが追加された
- **データ所在とガバナンス**：データは自分のプロジェクト内に留まり、自社ポリシーを適用可能。グローバル/リージョナルエンドポイントを可用性要件で選べる

## 5ペルソナ × Claude Code 構成要素

| ロール | やったこと | 使った機能 |
|---|---|---|
| **PM** | 手描きのワイヤーフレーム画像 → プロトタイプを描画 | CLAUDE.md に「PMとして画像からワイヤーフレームを起こす」指示 |
| **UI/UXデザイナー** | landing / thank-you / dashboard の3ページを本番品質UIへ | **plan mode**（実装前に計画を提示し承認できる）＋ Figma の design doc を MCP で参照 |
| **ソフトウェアエンジニア** | GCPの事前知識なしでアーキ設計＆デプロイ | **Developer Knowledge API＋MCPサーバー**（最新GCPドキュメントを供給し最適アーキを判断）＋ **Google Cloud Skills**（Cloud Runへのデプロイ、Cloud Run⇔Firestore接続など個別ブロックを担当）＋ **サブエージェント並列**（API/取り込みパイプライン/ダッシュボードを別エージェントで同時実装） |
| **セキュリティエンジニア** | リリース前レビューと自動修正 | Claude Code 同梱の **pre-built security review**（OWASP・サービスアカウントの最小権限）。問題を検出し自動修正 |
| **データ担当** | 収集データの分析・ダッシュボード化 | **BigQuery MCPサーバー**でクエリ、**MCP Toolbox for Databases**（OSSのMCPサーバー、Looker統合）でダッシュボード |

実装されたアーキテクチャ：フィードバックAPIを **Cloud Run**（サーバーレス）に、生レスポンスを **Firestore** に、分析用に **BigQuery**（DWH）へ蓄積、レポートを **Looker** で可視化。サブエージェントを「チームのスプリントのように」並列で走らせ、実装後のテストまで自動でこなす。

## Developer Knowledge API と Agent Registry

GCP側がagenticコーディングのエコシステムに合わせて出した2要素が鍵：

- **Developer Knowledge API（＋MCPサーバー）**：常に最新のGCPドキュメントをClaude Codeに直接供給。「GCPでどうデプロイするか」を人間が知らなくても、エージェントが最適なアーキ・実装を導く（アーキ設計＝MCP、個別実装＝Skills の役割分担）
- **Agent Platform / Agent Registry**：GCPがネイティブサポートするMCPサーバーの一覧（Developer Knowledge service、BigQuery MCP 等）。各サーバーのセットアップ方法・可観測性・ツール説明を提供

## 観察ログ（未検証）

- 2026-06-06: Google Cloud DevAdvocate の Ivan Nardini が登壇デモ（Anthropicとのパートナーシップで制作）。「ゼロからデプロイまで30分」のライブビルドとして提示。コードはセッション後に公開予定とのこと
- 2026-06-06: デモは強い単純化（security engineerがデプロイまで担う等、実運用とは異なる）と本人が明言。あくまで1シナリオの実演であり、各工程の品質・本番耐性は検証対象
- 2026-06-06: 講演中の口頭で製品名が "Cloud Code" "Co-Code" "Clockwork" などと揺れて文字起こしされているが、文脈上すべて Claude / Claude Code を指すと判断

## 問い

- 5ペルソナを1人＋Claude Codeで回す構成は、実際のエンタープライズで「品質ゲート」を満たせるか。security review の自動修正はどこまで信頼できるか。
- Developer Knowledge API（最新ドキュメントのMCP供給）は、自分のwikiの「最新情報をエージェントに渡す」課題に転用できる発想か。
- サブエージェント並列（API/パイプライン/ダッシュボード）は [[concepts/multi-agent-patterns]] のどのパターン（Fan-Out/Specialist Team）に当たるか。

## 関連

- [[tools/claude-code]] — 本パターンの実行基盤
- [[tools/google-agent-skills]] — デモで使われたGoogle Cloud製スキル群
- [[concepts/workload-identity-federation]] — ADCによるキーレス認証と同系統
- [[tools/claude-code-subagents]] — 並列実装に使ったサブエージェント
- [[concepts/multi-agent-patterns]] — 並列エージェント分業の設計パターン
