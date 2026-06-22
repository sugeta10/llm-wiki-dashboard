# Slack Bolt

Slack Appを構築するための公式フレームワーク。JavaScript（TypeScript）・Java・Pythonの3言語で提供。

## 概要

Slack APIのラッパーで、インタラクティブなSlackアプリ開発を大幅に簡略化する。Incoming Webhookより高度な操作（スレッド返信・ボタン応答・モーダル表示・スラッシュコマンド受信）を型安全に実装できる。

## 主な特徴

### Socket Mode対応

- 公開URLなしで開発環境からSlackと疎通できる
- `socketMode: true` の1設定のみで有効化

```typescript
const app = new App({
  token: process.env.BOT_TOKEN,
  appToken: process.env.SLACK_APP_TOKEN,
  socketMode: true,
});
```

- Cloud Run / AWS Lambda等での本番運用時はSocket ModeをオフにしてHTTP受信に切り替える（環境変数で切り替え可能）

### TypeScript型定義

- `ViewSubmitAction`・`InputBlock` 等、Slack APIのJSONに対応した型が用意されている
- 複雑なBlock KitのペイロードをJSON文字列なしで型安全に扱える
- 単体テストでSlackのリクエスト型を再現してロジックをテスト可能

### グローバルミドルウェア

リクエスト処理の前後に横断的な処理を挟める。OpenTelemetry計装の実装例：

```typescript
app.use(async ({ payload, next, logger }) => {
  await initializeSpan(rootSpanName, async () => {
    await next();
  });
});
```

### 日本語ドキュメント

3言語すべてで日本語ドキュメントが用意されている。

## 関連ライブラリ

- `node-slack-sdk` — Slack Web APIを単純に呼び出すだけならこちら。Bolt（App構築フレームワーク）とは用途が異なる

## 関連

- [[tools/claude-code]] — 類似のフレームワーク設計（CLI Appのミドルウェア構造）
