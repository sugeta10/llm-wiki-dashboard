# Claude Opus 5.5

[[companies/anthropic]] の Claude 系列で「Opus 5.5」の名を持つモデル。本vaultで捕捉できているのは、@nukonuko が2026-09-22に「Claude Opus 5.5 system prompts」として投稿したシステムプロンプトの冒頭部分だけで、Anthropic 公式の発表・価格・ベンチマークは未収集である。

捕捉できた冒頭は、`<claude_behavior>` の中に `<product_information>` という節があり、ユーザーに聞かれた場合に備えて Claude と Anthropic の製品情報を持たせる形になっている。そこには「現在選択されている Claude のバージョンは Claude Opus 5.5」「Claude Opus 5.5 は complex（複雑な〜）のための強力なモデル」と書かれ、文はここで途切れている。投稿者は個人で、プロンプトの入手経路は投稿に書かれておらず、公式が公開したものかどうかも確認できていない。

[[models/claude-opus-5]] の後継と考えられるが、系列内の位置づけを示す一次情報はまだ手元に無い。システムプロンプトをタグで節に分けて振る舞いを規定する作りは、Opus 5 移行時に利用者が実測した lean system prompt 化（[[concepts/lean-prompt-rules-adaptation]]）の続きとして読める可能性がある（推論）。

## 問い

- Anthropic 公式の発表で、Opus 5.5 の発表日・価格・Opus 5 との性能差を確認する
- 投稿されたシステムプロンプトの全文を入手し、Opus 5 時代の lean system prompt から何が増減したかを比べる。自分の CLAUDE.md・rules と噛み合わなくなる箇所があるか

## 関連

- [[models/claude-opus-5]] — 前世代と考えられる Opus（2026-07-24発表）
- [[concepts/lean-prompt-rules-adaptation]] — Opus 5 移行でシステムプロンプトが変わり旧 rules が噛み合わなくなった実測記録。本モデルのシステムプロンプトを読むときの比較対象
- [[companies/anthropic]] — 開発元
- [[concepts/effort-level-selection]] — @trq212 が Opus 5.5 で同一タスクを effort 別に実行した実験（low 0/5 → high 4〜5/5 の事例など）
