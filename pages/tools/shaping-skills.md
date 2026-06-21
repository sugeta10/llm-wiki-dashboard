Ryan Singer（Shape Up著者、元Basecamp）が作成したClaude Code向けスキル集。[Shape Up手法](https://basecamp.com/shapeup)のShaping（問題と解決策の反復）とBreadboarding（UI/コード/配線のマッピング）をLLMとの協働に適応させた4スキル。

## スキル一覧

**ドキュメントスキル（実プロジェクト向け）**

- **`/framing-doc`** — 会話のトランスクリプトを「解くべき問題とその選択理由」を捉えたFraming Documentに変換
- **`/kickoff-doc`** — Shaped projectのキックオフ議事を実装担当者向け参照ドキュメントに変換

> これらはGIGO（Garbage In, Garbage Out）。良い入力があって初めて時間を節約できる。

**ソロスキル（より実験的）**

- **`/shaping`** — 要件（What）と解決策（How）を分離しながら反復。フィットチェックで解決済み・未解決を追跡
- **`/breadboarding`** — システムをUIアフォーダンス・コードアフォーダンス・配線に分解して1つのビューで可視化。垂直スコープへの分割に有効

## Ripple Checkフック

`shaping: true` のfrontmatterを持つMarkdownを編集すると、Claudeがアフォーダンステーブル・フィットチェック・ワークストリームの更新を促すフックが自動起動する。

## インストール

```bash
git clone https://github.com/rjs/shaping-skills.git ~/.local/share/shaping-skills
ln -s ~/.local/share/shaping-skills/shaping ~/.claude/skills/shaping
# 他スキルも同様にシンボリックリンク
```

## 関連

- [[tools/claude-code]] — 実行プラットフォーム
- [[tools/compound-engineering]] — 同様の計画先行開発哲学を持つプラグイン
- [[concepts/spec-driven-development]] — 仕様先行開発の広い文脈
- [[tools/grill-me]] — 実装前の要件深掘り対話スキル
