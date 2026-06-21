# ccmd

**ccmd** は CLAUDE.md・AGENTS.md・.cursorrules・.grokrules を [[concepts/claude-md-rules|Karpathy 12ルール]]ルーブリックで採点するブラウザ内静的解析ツール。ファイルをどこにもアップロードせずブラウザ内で完結し、各行のトークンコストとフィンディングを可視化する。

関連: [[concepts/claude-md-rules]], [[tools/claude-code]]

## 主要機能

- **12ルール採点**: Think-Before-Coding, Simplicity-First, Surgical-Changes, Goal-Driven-Execution, Avoid-Silent-Assumptions, No-Orthogonal-Damage, Tests-as-Truth, Concise-Output, Stack-Awareness, Tool-Preference, Failure-Mode-Coverage, Self-Improvement-Loop
- **問題タイプ分類**: aspirational（"always"等の絶対命令）/ vague（"good"等の測定不能語）/ missing_why（禁止理由なし）
- **トークンコスト試算**: Opus 4.7レートでの30ターンセッションあたりコスト
- **4エコシステム対応**: CLAUDE.md（Claude Code）/ AGENTS.md（OpenAI Codex）/ .cursorrules（Cursor IDE）/ .grokrules（xAI Grok Build）

## 背景・差別化

既存ツールは「生成系（テンプレート生成）」か「コストメーター（消費量把握）」に二分される。ccmd はすでに書いたファイルを **過去の失敗モードに紐づいたルーブリックで採点する** 点が異なる。

> every single API call to Claude sends the whole context, including prompts, meaning that all this extra text in CLAUDE.md is sent over and over.
> — caymanjim, HN 47581701

Anthropic が 2026-06-15 から Claude Code エージェント利用を別クレジットプールに移行したことで、肥大化した CLAUDE.md は文字通り課金明細の一行になる。

## 制限・注意

- Free tier: ブラウザ内静的解析のみ（ルールが実際にどのターンで発火するかは分からない）
- Paid tier（未リリース）: GitHub webhook + 継続ドリフト監視 + チーム単位コスト集計
