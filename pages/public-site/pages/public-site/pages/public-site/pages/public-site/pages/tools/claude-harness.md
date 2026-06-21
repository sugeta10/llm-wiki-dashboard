# Claude Harness

[[tools/claude-code]] の上に構築される品質保証レイヤー。LLM単体では保証できないループ制御・評価・引き継ぎを担う。

## Hokage（vibecoder_japan 実装）

「Claude Harness - Hokage v4.0.0」は日本の開発者が公開した Claude ハーネスフレームワーク。

- Claude を「Hokage（火影）」として位置づけ、複数サブエージェントを指揮する構成
- Default-FAIL contract・Fresh-context evaluator・Agent-maintained handoff の3プリミティブを実装
- v4.0.0 時点でループ品質の大幅改善

## Anthropic 公式: cwc-long-running-agents

Anthropic が公開した長時間実行エージェント向けのハーネス設計パターン（GitHub: `anthropics/cwc-long-running-agents`）。

3つのコアプリミティブ：
1. **Default-FAIL contract** — `test-results.json` の全criterionは `false` 始まり。`track-read.sh` + `verify-gate.sh` フックがReadツールで証拠ファイルを開くまで書き込みをブロック
2. **Fresh-context evaluator** — `agents/evaluator.md` をWrite/Editなしで起動し、ビルドを見ていない新コンテキストで diff + スクリーンショットをレビュー。PASS/NEEDS_WORK を返す
3. **Agent-maintained handoff** — `PROGRESS.md` に進捗を記録し `commit-on-stop.sh` がセッション終了時にコミット。次セッションはgit logと PROGRESS.md から再開

### オペレーター制御フック

| フック | 役割 |
|--------|------|
| `kill-switch.sh` | プロジェクトルートに `AGENT_STOP` ファイルがある間、全ツール呼び出しを停止 |
| `steer.sh` | `STEER.md` の内容をエージェントに一度だけ渡してクリア（再起動不要の方向転換） |

### ループの観察（ターミナル）

```bash
watch -n 2 'tail -20 PROGRESS.md'          # エージェント自身のメモ
watch -n 5 'git log --oneline -8'           # コミット履歴
watch -n 5 'find screenshots -name "*.png"' # キャプチャ
watch -n 2 'wc -l < .claude/.evidence-reads'# 証拠読み込みカウント
```

### 発展パターン

| パターン | 概要 |
|----------|------|
| Unattended loop | セッション上限を設け、外部スクリプトが次セッションを起動（ralph-loop プラグイン） |
| Planner agent | 1行の要求を `BUILD_PLAN.md` に展開してからループ実行 |
| Sprint contracts | ビルダーと evaluator が feature ごとの「完了条件」をファイルに合意 |
| Grading rubrics | 機能・デザイン・クラフトに few-shot 採点基準を渡して二値判定を脱する |
| Browser-verified evaluator | evaluator が Playwright MCP でアプリを直接開いてスクリーンショットに依存しない |

> 各モデルリリース後は harness の各ピースをコメントアウトして「まだ必要か」を再評価する（Re-simplify on model upgrades）

## [[tools/claude-code]] との関係

Claude Code の5層アーキテクチャにおける **Harness layer**（第4層）に相当。プロンプトレベルの工夫ではなく、ループ制御の構造的な設計。

## 関連

- [[tools/claude-code]]
- [[concepts/agentic-coding]]
- [[concepts/self-refining-skills]]
- [[companies/anthropic]]
- [[tools/slack-bolt]] — SlackをAIエージェントのUI/トリガーとして使う場合の実装フレームワーク（Claude CodeやWorkatoへの接続起点）
- [[concepts/ai-engineer-roadmap]] — 「ハーネスが仕事の本体」とし、自前ミニハーネス1,500行を作る学習段階を置く2026ロードマップ
- [[concepts/harness-engineering]] — ハーネスの概念定義・5アーティファクト・3陣営・ハーネス崩壊（Build to Delete）の全体論
