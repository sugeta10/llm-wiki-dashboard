# Vibe Coding

**Vibe Coding** は [[people/andrej-karpathy]] が提唱したAI支援プログラミングのスタイル。コードの細部を自分で書かず、AIが生成するコードの「雰囲気（vibe）」を感じながら指示だけで開発を進めるアプローチ。

## 特徴

- 開発者はコードを直接書かず、自然言語で指示
- AIが実装・レビュー・修正のループを自律的に回す
- 細部の実装知識がなくても動くものを作れる

## 実践例

**Cursor × CodeRabbit ループ:**
1. [[tools/cursor]] がコードを書く
2. CodeRabbit の無料拡張がレビュー
3. Cursor が指摘を修正
4. 1〜3 を繰り返す → 人間は見ているだけ

## Karpathy の見解

[[people/andrej-karpathy]] 自身が「vibe coding を提唱した自分が最も遅れを感じている」と発言。Sequoiaでの30分講演で [[tools/claude-code]]・[[tools/cursor]]・Codexについて解説している。

## 関連

- [[people/andrej-karpathy]]
- [[tools/cursor]]
- [[tools/claude-code]]
- [[concepts/agentic-coding]]
- [[concepts/spec-driven-development]] — Vibe Codingの限界を受けて生まれた仕様駆動スタイル
- [[tools/google-ai-studio]] — 自然言語アプリ構築〜無料デプロイ・pretty URL配布までを受けるGoogleのプラットフォーム
