# Understand-Anything

コードベース全体を**対話型のナレッジグラフ**に変換するツール。未知の大規模コードでも、構造・依存関係・業務ロジックを地図のように可視化し、検索や自然言語での質問ができる。Claude Code・[[tools/cursor]]・GitHub Copilot・Gemini CLI に対応。

## 位置づけ

[[concepts/claude-code-large-codebase]] が示す通り、[[tools/claude-code]] 自体はコードベースをベクトル化せず、ファイルツリーをたどり grep で必要箇所を探す（=ライブのコードベースを見る）方式を取る。Understand-Anything はこれと対照的に、コードベースを事前にナレッジグラフ化して構造・依存・業務ロジックを俯瞰できるようにするアプローチ。AIコーディングエージェントの「全体像把握」の弱点を補う補助レイヤーとして使える。

## 観察ログ（未検証）

- 2026-05-31: @so_ainsight の「今週GitHubで急上昇したAI関連リポジトリ10選」の第1位として紹介（二次まとめ、Tier 3）。具体的なスター数・実装精度・対応規模の上限などは未確認。

## 問い

- 事前グラフ化方式は grep ベースのライブ探索（[[concepts/claude-code-large-codebase]]）に対して、大規模コードでの精度・鮮度でどちらが優位か？
- 自分のwiki/コードベースで「依存関係の地図」として使えるか。グラフの更新頻度（コード変更への追従）はどうなっているか。

## 関連

- [[concepts/claude-code-large-codebase]]
- [[tools/claude-code]]
- [[tools/cursor]]
