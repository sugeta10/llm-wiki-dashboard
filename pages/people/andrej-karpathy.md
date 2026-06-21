# Andrej Karpathy

AI研究者。元 OpenAI 共同創業者・Tesla AI 責任者。現在は独立して教育・研究活動を続ける。

## 主な貢献

- **「vibe coding」** という概念の提唱者（[[concepts/vibe-coding]]）
- **「LLM Wiki」** の提唱者 — LLM が継続的にメンテナンスする Markdown 知識ベース（[[concepts/llm-wiki]]）
- LLMと教育を結びつける活動（YouTube での深い解説動画シリーズ）
- Sequoia での講演でAIコーディングツールの現状を解説

## Vibe Coding に関する発言

vibe coding を提唱した本人が「今まで感じたことがないほど自分がプログラマーとして遅れを感じている」と発言。AIコーディングの進化速度の速さを示唆している。

## Sequoia 講演

30分の講演で [[tools/claude-code]]・[[tools/cursor]]・Codex について解説。vibe coding チュートリアル100本分以上の情報量と評される。

## Second Brain 実装

Karpathy の LLM Wiki 実装について NickSpisak_ が詳細解説（41,000件ブックマーク）：
- **3フォルダ**: `raw/`・`wiki/`・`outputs/`
- CLAUDE.md（AGENTS.md）でAIへの指示書を定義
- 「super simple and flat」— Obsidianも不要、フラットな .md ディレクトリで十分

## CLAUDE.md ルール設計への影響

2026年1月のスレッドでClaude のコーディング問題（サイレントな誤った前提・過剰複雑化・直交的破壊）を指摘。Forrest Chang がそれを4ルールに変換し120,000 stars を獲得。さらに8ルールが追加されて[[concepts/claude-md-rules]]として体系化された。

## 関連

- [[concepts/vibe-coding]]
- [[concepts/llm-wiki]]
- [[tools/claude-code]]
- [[tools/cursor]]
- [[companies/anthropic]]
- [[concepts/claude-md-rules]]
- [[tools/gstack]]（Karpathy の「コードをほぼ書いていない」発言が gstack 開発の直接的動機）
- [[concepts/llm-council]] — Karpathy が考案した LLM Council（複数モデルのポーリング＋匿名ピアレビュー）パターン
- [[concepts/attention]] — Transformer中核機構。Karpathyのコンテンツが追加学習先として推奨される
- [[concepts/research-methodology]] — Karpathyの「single batch overfit」「raw data を手で見る」技法が引用
