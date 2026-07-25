# Knowledge Work Plugins（Claude Desktop業務別公式プラグイン）

> **TL;DR**: Claude Desktopの「ディレクトリ→プラグイン→Anthropic」タブから選べる業務別公式プラグイン10種（Engineering/Product Management/Enterprise Search/Data/Design/PDF Viewer/Operations/Marketing/Legal/Small Business）は`anthropics/knowledge-work-plugins`マーケットプレイスとしてClaude Codeにも導入でき、@ai_ai_ailoverは最初の3本にEngineering・Product Management・Enterprise Searchを薦める。

[[tools/claude-code-plugins]] が扱う`claude-plugins-official`（開発者向け123プラグイン、CLI専用マーケットプレイス）とは別物で、こちらはClaude DesktopのChatやCoworkを主対象に配布されている業務別パッケージ。Anthropicの公式リポジトリでは同じパッケージをClaude Codeへも導入できると案内されており、スキルやスラッシュコマンド、MCPコネクタを開発ワークフローに持ち込める。@ai_ai_ailoverは、単に機能が多いかでなく開発者・PM・デザイナー・技術責任者・個人開発者が実務で繰り返し使えるかを基準に10本を選んだと述べる。

## 導入方法

Claude Desktopでは左サイドバー「カスタマイズ」→「プラグイン」タブ→「＋」でディレクトリを開き、「Anthropic」タブから対象カードの「＋」またはInstallを押す。Claude Codeでは公式マーケットプレイスを登録してから個別インストールする。

```text
claude plugin marketplace add anthropics/knowledge-work-plugins
claude plugin install engineering@knowledge-work-plugins
```

`engineering` の部分を `data`・`design`・`product-management` などへ変えれば他のプラグインも同様に導入できる。導入後はスキルが必要な場面で自動的に参照され、`/engineering:review`や`/data:write-query`のように名前空間付きで明示的に呼び出すこともできる。

## 10プラグインの概要

| プラグイン | 対象領域 | 代表コマンド |
|---|---|---|
| Engineering | スタンドアップ・コードレビュー・デバッグ・アーキテクチャ判断・インシデント対応・デプロイ前確認・技術ドキュメント | `/engineering:review`, `/engineering:debug`, `/engineering:architecture`, `/engineering:incident`, `/engineering:deploy-checklist` |
| Product Management | 仕様・PRD作成、ロードマップ、進捗報告、リサーチ統合、競合比較、指標点検（RICE/MoSCoW/JTBD/Opportunity Solution Tree等を内蔵） | `/product-management:write-spec`, `/product-management:roadmap-update`, `/product-management:stakeholder-update`, `/product-management:synthesize-research`, `/product-management:competitive-brief`, `/product-management:metrics-review`, `/product-management:brainstorm` |
| Enterprise Search | Slack・メール・クラウドストレージ・Wiki・プロジェクト管理・CRM・チケット管理を横断検索し出典付きで統合回答 | `/enterprise-search:search`, `/enterprise-search:digest --daily`/`--weekly` |
| Data | データ探索・品質確認・SQL生成・可視化・ダッシュボード作成・検証（Snowflake/Databricks/BigQuery等にMCP接続可、非接続でもCSV/Excel対応） | `/data:analyze`, `/data:explore-data`, `/data:write-query`, `/data:create-viz`, `/data:build-dashboard`, `/data:validate` |
| Design | デザイン批評・デザインシステム監査・UXライティング・アクセシビリティ監査・開発者向けハンドオフ | `/design:critique`, `/design:design-system`, `/design:handoff`, `/design:ux-copy`, `/design:accessibility`, `/design:research-synthesis` |
| PDF Viewer | PDFをインタラクティブビューアーで開き注釈・フォーム入力・署名画像配置（Node.js 18以上、`@modelcontextprotocol/server-pdf`をローカルMCPとして起動） | `/pdf-viewer:open`, `/pdf-viewer:annotate`, `/pdf-viewer:fill-form`, `/pdf-viewer:sign` |
| Operations | ベンダー評価・業務プロセス文書化・変更管理・キャパシティ計画・ランブック作成 | `/operations:vendor-review`, `/operations:process-doc`, `/operations:change-request`, `/operations:capacity-plan`, `/operations:runbook` |
| Marketing | ブログ・SNS・LP等のコンテンツ制作、キャンペーン計画、ブランド整合性確認（Slack/Canva/Figma/HubSpot/Amplitude/Notion/Ahrefs/Similarweb/Klaviyo等と連携想定） | `/marketing:draft-content`, `/marketing:campaign-plan`, `/marketing:brand-review`, `/marketing:competitive-brief`, `/marketing:performance-report`, `/marketing:seo-audit`, `/marketing:email-sequence` |
| Legal | 契約レビュー（`legal.local.md`に自社プレイブックを設定し照合）、NDA一次判定、コンプライアンス | `/legal:review-contract`, `/legal:triage-nda`, `/legal:vendor-check`, `/legal:brief`, `/legal:respond` |
| Small Business | 資金繰り・請求・価格・営業・苦情対応・週次レビューなど小規模事業運営（15基礎スキル+15実行ワークフロー+自然言語ルーター、QuickBooks/PayPal/HubSpot/Gmail等と連携想定） | `/small-business:plan-payroll`, `/small-business:month-heads-up`, `/small-business:close-month`, `/small-business:price-check`, `/small-business:run-campaign`, `/small-business:handle-complaint`, `/small-business:monday-brief` |

@ai_ai_ailoverは目的別の組み合わせも提案している。一人開発者にはEngineering・Product Management・Design・PDF Viewer・Small Business、複数人チームにはEngineering・Enterprise Search・Data・Operations・Legal、プロダクト立ち上げ・大型リリースにはProduct Management・Design・Engineering・Marketingが効くという。

## 導入時の注意点（@ai_ai_ailover）

- Desktop版とClaude Code版で導入手順が異なる（前者はディレクトリから追加、後者はマーケットプレイス登録）ため混同しない
- コネクタは「便利な検索先」でなく権限を持つ外部連携であり、読み取りだけで足りる作業に書き込み権限を与えない。会計・決済・メール・契約・個人情報は範囲を絞り、送信・更新・承認を伴う処理には人間の確認を置く
- 一度に入れすぎない。自分のボトルネックに直接効く2〜3本から始める
- 出力は「完成品」でなく「検証可能な下書き」として扱う。レビュー結果をテストやCIの代わりにしない、ランブックはリハーサルする、契約は専門家が確認する、など承認責任まではAIに移せない

## 観察ログ（未検証）

- 2026-07-18時点: プラグインはPro、Max、Team、Enterpriseの有料プランで利用可能（@ai_ai_ailover談。無料プランでの可否は記載なし）

## 問い

- 実際にClaude Codeへ`anthropics/knowledge-work-plugins`を導入した場合、[[tools/claude-code-plugins]]の`claude-plugins-official`と併用してコマンド名の衝突や権限設定の競合は起きないか
- Enterprise Searchのアクセス権スコープ設定（Slack/ドキュメント/プロジェクト管理から段階的に接続を広げる運用）は自分の環境でどう最小権限設計するか

## 関連

- [[tools/claude-code-plugins]] — 同じAnthropic配布だが別マーケットプレイス（開発者向け123プラグイン、CLI専用）。本ページの`knowledge-work-plugins`は業務別10プラグインでDesktop/Cowork主体
- [[tools/claude-code]] — Claude Code本体
- [[tools/claude-mcp]] — 各プラグインが外部サービス接続に使うMCPコネクタの仕組み
