# gstack

[[people/garry-tan]]（Y Combinator CEO）が作成したオープンソースの**AIソフトウェアファクトリー**。[[tools/claude-code]]を「バーチャルエンジニアリングチーム」に変換する23以上のスラッシュコマンド集。MIT ライセンス、無料。

## 背景・動機

Garry Tan の 2026 年コーディング生産性は 2013 年比で **~810倍**（論理コード行数ベース）。2026年のYTD（4月18日時点）は2013年年間の240倍。AI が書いたコードが大半だが「誰が打ち込んだかではなく、何をリリースしたかが重要」という哲学。

> "I don't think I've typed like a line of code probably since December" — [[people/andrej-karpathy]]（No Priors podcast, 2026年3月）

この Karpathy の発言が gstack 開発の直接的動機。

## スプリントプロセス

**Think → Plan → Build → Review → Test → Ship → Reflect**

各スキルが連鎖する設計。`/office-hours` が書いたデザインドキュメントを `/plan-ceo-review` が読み込む。ステップ間でコンテキストが継承されるため「抜け落ちがない」。

## 主要スキル一覧

| スキル | 役割 | 概要 |
|-------|------|------|
| `/office-hours` | YC Office Hours | 6つの強制質問でフレーミングを再定義。コーディング前に仮説を壊す |
| `/plan-ceo-review` | CEO / Founder | 問題の本質を再定義。4モード（拡張・選択拡張・スコープ維持・縮小） |
| `/plan-eng-review` | Eng Manager | アーキテクチャ・データフロー・エッジケース・テスト計画を確定 |
| `/plan-design-review` | Senior Designer | 各デザイン次元を 0-10 で評価し、10点の姿を解説してから編集 |
| `/autoplan` | Review Pipeline | CEO → Design → Eng レビューを1コマンドで自動実行 |
| `/review` | Staff Engineer | CI を通過するがプロダクションで壊れるバグを発見。明白なものは自動修正 |
| `/investigate` | Debugger | 体系的な根本原因デバッグ。「鉄則: 調査なしの修正禁止」 |
| `/qa` | QA Lead | リアルブラウザでテスト → バグ発見 → 修正 → 再確認。リグレッションテストを自動生成 |
| `/cso` | Chief Security Officer | OWASP Top 10 + STRIDE 脅威モデル。17の誤検知除外・信頼度8/10以上ゲート |
| `/ship` | Release Engineer | テスト実行・カバレッジ監査・プッシュ・PR作成。テストフレームワーク未設定なら自動構築 |
| `/browse` | QA Engineer | 実 Chromium ブラウザを操作。~100ms/コマンド |
| `/design-shotgun` | Design Explorer | 4-6種のAIモックアップ変種を生成 → ブラウザで比較 → 反復 |
| `/design-html` | Design Engineer | モックアップを本番品質 HTML/CSS に変換（Pretext使用・30KB・ゼロ依存） |
| `/retro` | Eng Manager | 週次レトロスペクティブ。`/retro global` で全プロジェクトを横断集計 |

## OpenClaw との連携

gstack のスキルは [[tools/openclaw]] 経由で Claude Code セッションを spawn する際にも動作する。ClawHub 経由で会話スキルも4種インストール可能。

ディスパッチ例：
- 「セキュリティ監査実行」→ Claude Code セッションに `Run /cso` を渡す
- 「通知機能を作って」→ `/autoplan` → 実装 → `/ship` の順で自動進行

## 対応AIエージェント

Claude Code だけでなく10種のAIエージェントに対応（`./setup --host <name>` で切り替え）：
OpenAI Codex CLI, OpenCode, Cursor, Factory Droid, Slate, Kiro, Hermes, GBrain 等。

## GBrain

永続知識ベース。セッション間でエージェントの記憶を保持する仕組み。`/setup-gbrain` で3種から選択：
- **Supabase（自動プロビジョニング）**: PAT を貼るだけ、~90秒で完了
- **Supabase（既存URL）**: 複数マシン間で記憶を共有
- **PGLite（ローカル）**: アカウント不要・~30秒

## 並列スプリント

10〜15の並列スプリントを [Conductor](https://conductor.build/) で管理する使い方を Garry Tan 自身が実践。「プロセスがあるから並列化が機能する。プロセスなしの10エージェントは10のカオス源になる」。

## セキュリティ

- **プロンプトインジェクション防御**: 22MB ML 分類器 + Claude Haiku トランスクリプト検査 + ランダムカナリートークン
- `/careful`: 破壊的コマンド（rm -rf, DROP TABLE, force-push）前に警告
- `/freeze`: 特定ディレクトリ外への編集を禁止
- `/guard`: `/careful` + `/freeze` を同時有効化

## 関連

- [[tools/claude-code]]
- [[tools/openclaw]]
- [[people/garry-tan]]
- [[people/andrej-karpathy]]
- [[concepts/agentic-coding]]
- [[concepts/vibe-coding]]
