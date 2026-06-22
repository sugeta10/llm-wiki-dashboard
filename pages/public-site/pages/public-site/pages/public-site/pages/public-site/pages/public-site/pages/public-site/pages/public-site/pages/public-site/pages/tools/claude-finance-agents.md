# Claude Finance Agents

[[companies/anthropic]] が金融サービス向けに公開した10種類のエージェントテンプレート群（2026年5月）。Claude Cowork・Claude Code のプラグイン、および [[tools/claude-managed-agents]] のクックブックとして提供。

## エージェントテンプレート一覧

### リサーチ・クライアント対応

| エージェント | 機能 |
|------------|------|
| **Pitch builder** | ターゲットリスト作成・コンプ分析・ピッチブックドラフト |
| **Meeting preparer** | クライアント・取引先ブリーフ作成 |
| **Earnings reviewer** | 決算書・開示資料読み込み、モデル更新、論点フラグ |
| **Model builder** | 開示資料・データフィードからファイナンシャルモデル構築・保守 |
| **Market researcher** | セクター動向トラッキング、ニュース・ブローカーレポート統合 |

### ファイナンス・オペレーション

| エージェント | 機能 |
|------------|------|
| **Valuation reviewer** | コンプ・方法論・社内基準に照らしたバリュエーション確認 |
| **General ledger reconciler** | 総勘定元帳の照合・NAV計算 |
| **Month-end closer** | クローズチェックリスト実行・仕訳作成・クローズレポート出力 |
| **Statement auditor** | 財務諸表の整合性・完全性・監査対応審査 |
| **KYC screener** | エンティティファイル作成・ソース文書審査・エスカレーションパッケージング |

## 2つの利用モード

### プラグイン（Claude Cowork / Claude Code）

- アナリストのデスクトップ上で既存ソフトと並走
- 例: Pitch agentにターゲットリストを渡す → ExcelコンプモデルとPowerPointピッチブックとOutlook下書きが返ってくる

### Claude Managed Agent（自律実行）

[[tools/claude-managed-agents]] プラットフォーム上で自律実行：

- 長時間セッション（数時間規模のディールクローズに対応）
- ツール単位の権限管理・認証情報の保管庫
- Claude Console に全ツール呼び出し・判断の完全監査ログ
- 帳簿全体・夜間スケジュール実行に対応

## Microsoft 365 統合

Claude for Excel / PowerPoint / Word / Outlook アドインにより4アプリを横断：

- **Excel**: 開示資料からのモデル構築・数式監査・感度分析
- **PowerPoint**: 数値が変わると自動更新されるデッキドラフト
- **Word**: 社内テンプレートに沿ったクレジットメモ編集
- **Outlook**: 受信トレイトリアージ・会議調整・返信ドラフト（近日公開）

**コンテキスト引き継ぎ**: Excelで始めたモデルをPowerPointに移す際、再説明不要。

## データコネクタ（エコシステム）

既存パートナー: FactSet, S&P Capital IQ, MSCI, PitchBook, Morningstar, Chronograph, LSEG, Daloopa

新規追加コネクタ：
- **Dun & Bradstreet** — ビジネスID検証・AI対応ワークフロー
- **Fiscal AI** — 公開株式のリアルタイムファンダメンタルズ
- **Financial Modeling Prep** — 株式・ETF・仮想通貨・外国為替のリアルタイムデータ
- **Guidepoint** — 10万件超のコンプライアンス審査済みエキスパートインタビュー
- **IBISWorld** — 数千業種の業界収益・財務比率・リスクスコア
- **SS&C Intralinks** — DealCenter AIデータルーム（ドキュメント検索・デューデリ）
- **Third Bridge** — 一次情報エキスパートインタビュー
- **Verisk** — 損保・専門保険のアンダーライティング・クレームデータ

**MCPアプリ**: Moody's が60億超の企業に対する独自信用格付け・データを提供。

## 推奨モデル

**Claude Opus 4.7** — 金融タスク特化。Vals AI Finance Agentベンチマーク業界首位 (**64.37%**)。

## 関連

- [[companies/anthropic]]
- [[tools/claude-managed-agents]]
- [[tools/claude-mcp]]
- [[business/backoffice-ai-implementation]]
- [[tools/autohedge]] — 自律型ヘッジファンドOSS（4エージェント分業）。公式の本テンプレートと役割設計を対比できる
