# autoreview スキル

> **TL;DR**: ローカル変更・ブランチ・PRに対し、検証済みの実行可能な指摘だけを構造化して返すコードレビュー自動化スキル。デフォルトエンジンは Codex で、複数エンジンのレビューパネルにも対応する。

自動レビューの出力は信用できないという前提から設計されている。だから [[tools/openclaw]] の agent-skills リポジトリに含まれるこのスキルでは、レビュー結果はあくまで「助言」と位置づけ、すべての指摘を実際のコードパスで検証してから採用する。非現実的なエッジケース・投機的リスク・広範なリファクタリング提案は拒否され、アクション可能な指摘だけが残る。用途はクローズアウトチェック（着地前の最終確認）。

**レビュー対象モード**は3種類あり、レビューしたい差分の単位で選ぶ。

| モード | コマンド例 | 用途 |
|--------|-----------|------|
| ローカル変更 | `autoreview --mode local` | ステージ済み・未ステージの変更 |
| ブランチ/PR | `autoreview --mode branch --base origin/main` | ブランチとの差分 |
| 単一コミット | `autoreview --mode commit --commit HEAD` | 着地済み・push済み作業 |

**エンジン**はデフォルトが Codex（最良結果）で、環境変数 `AUTOREVIEW_ENGINE` で切り替えられる。`--reviewers codex,claude` または `--panel` を指定すると、複数エンジンを1つの「冷凍バンドル（固定された差分スナップショット）」に対して並行して走らせるレビューパネルになる。

```
autoreview --reviewers codex,claude --model codex=gpt-5.1 --thinking codex=high --model claude=sonnet --thinking claude=max
```

`--thinking` は各エンジンの推論強度にマッピングされる。Codex は `model_reasoning_effort`（low/medium/high/xhigh）、Claude は `--effort`（max まで対応）に対応する。

**実行とテストの連携**: `--parallel-tests` を付けるとテストとレビューを並行実行し、どちらかが変更を引き起こした場合は再実行する。大きなバンドルでは最大30分間実行可能で、30分未満のサイレント期間ではプロセスを強制終了しない。`--stream-engine-output` でライブのエンジン出力をストリーミングでき、Codex/Claude はツールイベントとファイルイベントをフィルタリングしてコンパクトなサマリーを出力する。

**出力の挙動**: ヘルパーは stdout のみに出力し、`--output` / `--json-output` を指定しない限りファイルを書かない。セキュリティ監査の指摘を抑制する変更を行う場合でも、「抑制済み指摘」を構造化出力に残すことで、有効な出力が無関係なリスクを隠さないよう保証する。

**スキルが守る契約（Contract）**:

- 指摘はすべて実コードで検証してから適用する
- セキュリティ指摘は具体的・実行可能なリスクのみ報告する
- ネストされたレビュー・別のエンジン呼び出しは禁止（ヘルパー内部で行う）
- アクション可能な指摘がゼロで exit 0 が出たら停止する。追加の確認レビューは不要

## 検証済み事実

- 2026-05-28: デフォルトエンジンは Codex（環境変数 `AUTOREVIEW_ENGINE` で切替可能）
- 2026-05-28: `--reviewers codex,claude` または `--panel` で複数エンジンレビューパネルを実行できる
- 2026-05-28: ヘルパーは stdout のみに出力し、`--output` / `--json-output` 指定時以外はファイルを書かない

## 問い

- このレビュー検証フロー（指摘を実コードで検証してから採用）を自分のwiki運用やレビュー作業に転用できるか
- [[concepts/google-code-review]] の人手レビュー原則と autoreview の自動レビューはどこで役割分担すべきか

## 関連

- [[tools/openclaw]]
- [[tools/openclaw-agent-skills]] — autoreviewを同梱する共有スキルリポジトリ（正本・zero-setupスナップショット候補）
- [[concepts/google-code-review]]
- [[tools/claude-code]]
- [[tools/codex-review-skill]] — Codex内でレビュー→修正→合格まで反復するループ型レビューゲート（本スキルの検証済み指摘パネル型と対照）
