# xurl

X Developer Platform が公式に提供する X API v2 専用 CLI ツール。Go製・MITライセンス。[[tools/claude-code]] や [[concepts/agentic-coding]] との親和性が高い。

## 概要

「X API v2専用のcurl」。OAuthヘッダーの組み立て・エンドポイント調査・JSON生成を自動化し、ターミナルからコマンド一発でX操作が可能。

## v1.0.3 主要機能（2026年2月）

### Agent-friendly shortcuts

```bash
xurl post "こんにちは！"
xurl media upload 画像.jpg
xurl reply 1234567890 "返信だよ"
```

長いJSONを書かずに済む。エージェントがツールコールで自然に呼びやすい設計になっている。

### App・User 管理の大幅改善

- 複数アプリ／ユーザーの登録・切り替えが容易
- `--app myapp --username @user` フラグで即指定
- 一度認証したらトークンを意識不要（persistent storage）

### agentskills.io 統合・OpenClaw マージ

xurl 自体が **SKILL protocol** 対応スキルとして [agentskills.io](https://agentskills.io) に登録。**OpenClaw**（エージェントフレームワーク）に正式マージ。LLMがトークンや認証を意識せずXを操れる。

## インストール（Mac）

```bash
brew install --cask xdevplatform/tap/xurl
```

認証セットアップ（X Developer Portalでアプリ作成後）：

```bash
xurl auth apps add my-app --client-id CLIENT_ID --client-secret CLIENT_SECRET
xurl auth default my-app
xurl auth oauth2
```

## [[tools/claude-code]] との連携

```bash
# 検索結果をJSONで取得して分析パイプラインに流す
xurl search "AI agent" -n 100 > results.json

# スレッド投稿の自動化
ID=$(xurl post "1/3: スレッドの最初" | jq -r '.data.id')
ID2=$(xurl reply $ID "2/3: 続き" | jq -r '.data.id')
xurl reply $ID2 "3/3: 最後"
```

Claude Code + xurl で「記事を書いて → Xへポスト」がターミナル完結。定期的なアカウント分析や自動投稿パイプラインを構築可能。

## 関連

- [[tools/claude-code]]
- [[concepts/agentic-coding]]
- [[concepts/x-algorithm-phoenix]] — 2026年5月のX公式アルゴリズム（Phoenix）変更：投稿戦略の転換点
- [[tools/ant-cli]] — Claude Platform 用の公式API専用CLI（xurlのClaude版にあたる）
