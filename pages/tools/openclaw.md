# OpenClaw

オープンソースのAIエージェント基盤。非同期タスク管理・モデル非依存・スキルズ（作業テンプレート）・定期実行を組み合わせた「24時間稼働するAI組織」を構築できる。GitHubスター21万超、フォーク4万、コントリビューター700人以上。

## 5層アーキテクチャ

| 層 | 役割 |
|----|------|
| **第1層: 体験層** | Discord・LINE・Slack・WhatsApp等の会話インターフェース |
| **第2層: ゲートウェイ層** | OpenClawの非同期タスク交通整理（⭐革命的） |
| **第3層: オーケストレーション層** | クーロン（定期実行）・スキルズ |
| **第4層: エージェント層** | Claude Code・Codex・Gemini等 |
| **第5層: ツール層** | MCP・CDP（ブラウザ直接操作） |

## 主要コンポーネント

### スキルズ（Skills）
- 膨大な作業手順をMarkdownファイル化した再利用可能テンプレート
- 「うまくいった作業を手順まとめてスキル化して」で自動保存
- ClawHubからの外部スキルは悪意あるスキルが混在する事例があるため**自作推奨**

### クーロン（Cron）
- 指定間隔での自動タスク実行（動画投稿・記事作成等）
- Unix cron設定もAIが自動生成

### ハートビート（Heartbeat）
- 30分ごとに自律的に次の行動を判断する定期プロセス
- 人間の介入なしで24時間稼働し続ける

## CDP vs MCP：ブラウザ操作の違い

| 方式 | 仕組み | 精度 |
|------|-------|------|
| **MCP経由**（Claude Code等） | Chrome MCPツール経由、抽象化レイヤーあり | ムラあり |
| **CDP経由**（OpenClaw） | Chrome DevTools Protocolで直接操作 | 高精度 |

CDPを直接操作するためTikTokのカバー画像位置選択等の細かいUI操作も可能。

## 特徴

- **モデル非依存**: Claude Code・Codex・Geminiを切り替え可能。[[models/claude-opus-4-7]]等の最新モデルへいつでも移行できる
- **記憶の永続化**: 短期・長期記憶をMarkdownで保持。ハイブリッド検索で効率的に引き出す
- **設計の再利用性**: 「ツールは入れ替わるけど設計は残る」— スキルズとして蓄積された設計資産はモデルが変わっても活用可能

## 活用例（マーケティング自動化）

Discordチャンネルに1行送信するだけで：
- Remotion + Gemini TTS + OpenAI Whisperで動画生成（1本あたり約1円以下）
- YouTube / TikTok / Instagramへ自動アップロード
- Qiita記事の下書き自動生成・定期投稿

## セキュリティ対策

- メインPCではなく**サブPC or VPS**で運用（Xサーバーが月額約1,000円のイメージ提供）
- プロンプトインジェクション対策プロンプトを設定
- Chrome Remote Desktopで安全に遠隔操作
- 外部スキル（ClawHub）は使わず自作推奨

## コスト

- OpenClaw自体はオープンソースで**無料**
- モデル利用にはサブスク必要: Claude Max約3万円/月、またはOpenAI Codex等

## 関連

- [[tools/claude-code]]
- [[tools/gstack]]（gstack スキルは OpenClaw セッション経由でも動作する）
- [[tools/notebooklm-py]]
- [[concepts/agentic-coding]]
- [[tools/claude-managed-agents]]
- [[tools/hermes-agent]] — 直接競合。アーキテクチャ哲学が真逆（学習エージェントGW包含 vs エージェントGW包含）
- [[tools/crabbox]] — CrabboxはOpenClawのネイティブプラグインとして動作（リモートボックスへのrsync+実行）
- [[tools/openclaw-agent-skills]] — OpenClaw向け共有スキルリポジトリ（autoreview/crabbox/handoff等の正本・symlink配布）
- [[tools/autoreview-skill]] — OpenClaw agent-skills に含まれるコードレビュー自動化スキル
- [[concepts/agent-memory-layer]] — @pejmanjohn が OpenClaw を個人アシスタント/アイデア発展の場として使い、推論が他エージェントに同期されない問題を論じた文脈
- [[tools/codexbar]] — 同じ @steipete 製。Codex/Claude Code の使用制限残量をメニューバー常駐で表示
- [[tools/oh-my-openagent]] — メンテナAI Jobdori が OpenClaw の heavily customized fork 上で動作（Sisyphus Labs の運用基盤）
