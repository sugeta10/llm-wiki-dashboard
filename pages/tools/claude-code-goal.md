# Claude Code /goal コマンド

**`/goal`** は [[tools/claude-code]] のセッションスコープ自律ワークフロー機能。完了条件を設定すると、Claude が条件を満たすまで自動でターンを繰り返す。

## 概要

`/goal` の後に満たしたい条件を記述する：

```text
/goal all tests in test/auth pass and the lint step is clean
```

設定後は即座にターンが開始され、各ターン終了後に小さく高速なモデル（デフォルト: Haiku）が条件を評価する。条件が満たされるとゴールは自動でクリアされる。

## 他の自律ワークフローとの比較

| アプローチ | 次のターン開始タイミング | 停止条件 |
|-----------|----------------------|---------|
| `/goal` | 前のターン終了後 | モデルが条件充足を確認 |
| `/loop` | 時間間隔経過後 | 手動停止 or Claude判断 |
| Stop hook | 前のターン終了後 | 独自スクリプト/プロンプト |

- `/goal` と Stop hook は相互補完：`/goal` はセッション限定のショートカット、Stop hook は全セッション適用
- **自動モード**（auto-mode）との組み合わせが強力：自動モードはツール呼び出しごとのプロンプトを削除、`/goal` はターンごとのプロンプトを削除

## 効果的な条件の書き方

条件は最大 4,000 文字。評価器は Claude の出力から判断するため、Claude が実証できる形で書く：

- **1つの測定可能な終了状態**：テスト結果、ビルド終了コード、空のキュー
- **述べられたチェック**：「`npm test` が 0 で終了する」「`git status` がクリーン」
- **重要な制約**：変更してはならないものを明示

ターン・時間の上限も設定可能：

```text
/goal all tests pass or stop after 20 turns
```

## 主要コマンド

```text
/goal <条件>          # ゴール設定（既存は上書き）
/goal                 # ステータス確認（ターン数・トークン消費・理由）
/goal clear           # 手動クリア（stop/off/reset/cancel も可）
```

## 非対話的実行

`-p` フラグと組み合わせて非対話的モードで動作する：

```bash
claude -p "/goal CHANGELOG.md has an entry for every PR merged this week"
```

ループが完了条件を満たすまで単一の呼び出しで継続実行される。Ctrl+C で中断可能。

## 評価の仕組み

セッションスコープの**プロンプトベース Stop hook** のラッパー。ターン終了ごとに条件と会話履歴を評価モデルへ送信：

- **Yes** → ゴールクリア、トランスクリプトに記録
- **No** → 理由を次ターンのガイダンスとして Claude へ渡す

評価トークンはメインターンと比べて無視できる程度。

## セッション復元

`--resume` / `--continue` でセッション再開時、アクティブなゴールが復元される。ターン数・タイマー・トークン支出ベースラインはリセットされる。

## 関連

- [[tools/claude-code]] — Claude Code 本体
- [[concepts/agentic-coding]] — AIエージェントの自律実行スタイル
- [[concepts/claude-code-dynamic-workflows]] — `/goal`（ハード完了要件）・`/loop` と組み合わせてworkflowを強化する
- [[concepts/loop-engineering]] — /goal がループエンジニアリング Stage 4 の具体実装として位置づけられる
- [[concepts/goal-loop-routine]] — goal/loop/routine の動詞の使い分け。/goal は「条件を満たすまで走って止まる」動詞
- [[models/claude-fable-5]] — `/goal` にゴールだけ与えて放置し、全6コース54レッスンの教材サイト「言語の庭」が自走で完成した実証事例（水島宏太）
- [[concepts/frontier-model-extraction]] — フロンティアモデルの持久力を抽出する型4として `/goal`＋動的ワークフローを使う（貼られた証拠＋ハード上限の2安全ルール）
