# Claude Opus 4.7

[[companies/anthropic]] の最上位モデル。2026年4月16日リリース。コーディング・画像認識・エージェント系ベンチマークで首位を奪還。

## 主要ベンチマーク

| ベンチマーク | スコア | 変化 |
|------------|-------|------|
| SWE-bench Verified | 87.6% | +6.8pt |
| CursorBench | 70% | +12pt |
| MCP-Atlas | 77.3% | GPT-5.4比 +9pt |
| Finance Agent v1.1 | 64.4% | — |
| 画像認識（XBOW UI click） | 98.5% | +44pt（ほぼ人間レベル） |
| Rakuten-SWE-Bench | 解決数3倍、品質二桁改善 | — |

## 料金（1Mトークンあたり）

- 入力: $5.00 / 出力: $25.00（Opus 4.6から据え置き）
- **実質値上げ注意**: 新tokenizerにより同じ文でも消費トークンが最大 **1.35倍** に増加
- 英語・コード中心のワークロードほど影響大。日本語中心は誤差レベル
- プロンプトキャッシュ読み取り: 入力の10%
- バッチAPI: 入出力ともに50%オフ

## 画像認識の大幅向上

XBOWベンチで 54.5% → 98.5%（+44pt）。最大画像解像度は長辺2,576px（従来比3倍超、A4相当の画面を丸ごと読める）。スクリーンショットベースのUI操作自動化が実用レベルに到達。

## Opus 4.7向けプロンプト変更点

Opus 4.7は「指示されたことを忠実にやる」方向に調整されており、以前のプロンプトがそのまま使えない場合がある：

1. **指示は「書き切り」が前提**: 「全体的に」「いい感じに」は伝わりにくい。適用範囲を明示する
2. **トーン・口調は明示で再現**: warmな応答に依存したプロンプトは「親しみやすく共感的な口調で」のように明示が必要
3. **知能要求の高い用途は `effort` を上げる**: コーディング・エージェント用途は `xhigh`、それ以外の高度な用途は `high` 以上推奨

## 2026-05-13 GA発表：追加情報

### Mythos Previewとの位置づけ

[[models/claude-mythos]]（Mythos Preview）が最高能力・最高安全性のモデルだが、サイバー能力の懸念から一般公開を限定。Opus 4.7は新しいサイバーセーフガードを試験した最初のモデルとして先に一般公開された。

### 新機能・API変更

- **xhigh effort**: `high` と `max` の間の新effortレベル。Claude Codeではデフォルトを `xhigh` に変更
- **task budgets**（public beta）: 長時間実行でのトークン消費をコントロール
- **`/ultrareview`**: Claude Codeの新スラッシュコマンド。変更をレビューしてバグ・設計問題をフラグ。Pro/Maxユーザーに3回無料トライアル
- **auto mode**: Max ユーザーに拡張。Claudeが自律的にパーミッション判断して長時間タスクを実行

### 追加ベンチマーク（GA版）

| ベンチマーク | スコア |
|------------|-------|
| GDPval-AA（法律・財務等の経済価値知識業務） | SOTA |
| CyberGym | 73.8%（Opus 4.6から更新） |
| SWE-bench Multilingual | Opus 4.6比改善 |

### ファイルシステムベースメモリ

長期・マルチセッション作業でのメモリ運用が改善。重要メモを複数セッションをまたいで記憶し、次タスクで参照できる。

### サイバーセキュリティ用途

セキュリティ専門家向けに **Cyber Verification Program** を設置。申請すると脆弱性リサーチ・ペネトレーションテスト・レッドチーム用途でOpus 4.7を利用可能。

### 安全性評価

- 欺瞞・追従・誤用への協力は低率（Opus 4.6と同等）
- 誠実性・プロンプトインジェクション耐性はOpus 4.6より改善
- Mythos Previewが依然として最も低い誤整合行動率

## 提供プラットフォーム

- Claude.ai 全プロダクト・API（`claude-opus-4-7`）
- Amazon Bedrock
- Google Cloud Vertex AI
- Microsoft Foundry

## 推奨用途

- コーディング × 長時間エージェント × PC操作
- 画像・スクリーンショットベースの操作自動化
- 法律・財務など経済価値の高い知識業務（GDPval-AA SOTA）

コスト最優先の定常タスクは **Sonnet 4.6**（約40%安）が現時点の推奨。

## Claude Code × Opus 4.7 ベストプラクティス（追加情報）

- 2026-05-21: Algomatic AILabがAnthropicの公式ベストプラクティスを解説。要点: ① tokenizerが更新されており旧Opusとトークン数が異なる ② より深く思考するよう設計されているため、プロンプト構造や `effort` 設定の見直しが必要 ③ Claude Codeでの利用時は上記「Opus 4.7向けプロンプト変更点」セクションの3点が特に重要

## 関連

- [[companies/anthropic]]
- [[tools/claude-code]]
- [[tools/claude-managed-agents]]
- [[models/claude-mythos]]
- [[models/claude-opus-4-8]]
- [[concepts/prompt-engineering]]
- [[tools/hyperframes]]（HTML→動画OSS。実用化の土台となったコード生成モデル世代）
