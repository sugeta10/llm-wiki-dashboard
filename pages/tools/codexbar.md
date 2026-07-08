# CodexBar

**CodexBar** は、[[tools/openai-codex]]（Codex CLI）と [[tools/claude-code]] の**使用制限（レートリミット）の残量をメニューバー常駐で表示する macOS アプリ**。@steipete（Peter Steinberger、[[tools/openclaw]] の開発者）が製作し、GitHub で公開している。@taiyo_ai_gakuse が「PC 上のどこでも Codex と Claude Code の利用制限が見られる」と便利さを紹介した。

CLI で作業していると使用枠をどれだけ消費したかが見えにくく、いきなり制限に当たって作業が止まる。CodexBar はその残量を画面隅に常時可視化することで、[[concepts/claude-usage-optimization]] が説く「制限を計画的に避ける」運用を、頭の中の管理でなく UI で支える。

- リポジトリ: github.com/steipete/CodexBar
- 同じ @steipete 製のエージェント運用ツールに [[tools/skill-cleaner]]（スキル監査 CLI）、[[tools/oracle]]（Codex から上位モデル呼び出し）がある

## 問い

- 使用枠の可視化は本当に「制限に当たって止まる」を減らすか、それとも枠を意識しすぎて萎縮するか。
- Claude/Codex を並行運用するとき、残量表示は [[concepts/llm-model-selection-strategy]] のような工程別モデル切り替えの判断材料になるか。

## 関連

- [[tools/openai-codex]]
- [[tools/claude-code]]
- [[concepts/claude-usage-optimization]]
- [[tools/openclaw]]
- [[tools/skill-cleaner]]
- [[tools/oracle]]
