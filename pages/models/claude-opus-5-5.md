# Claude Opus 5.5

[[companies/anthropic]] の Claude 系列で「Opus 5.5」の名を持つモデル。本vaultで捕捉できているのは、@nukonuko が2026-09-22に「Claude Opus 5.5 system prompts」として投稿したシステムプロンプトの冒頭部分だけで、Anthropic 公式の発表・価格・ベンチマークは未収集である。

捕捉できた冒頭は、`<claude_behavior>` の中に `<product_information>` という節があり、ユーザーに聞かれた場合に備えて Claude と Anthropic の製品情報を持たせる形になっている。そこには「現在選択されている Claude のバージョンは Claude Opus 5.5」「Claude Opus 5.5 は complex（複雑な〜）のための強力なモデル」と書かれ、文はここで途切れている。投稿者は個人で、プロンプトの入手経路は投稿に書かれておらず、公式が公開したものかどうかも確認できていない。

[[models/claude-opus-5]] の後継と考えられるが、系列内の位置づけを示す一次情報はまだ手元に無い。システムプロンプトをタグで節に分けて振る舞いを規定する作りは、Opus 5 移行時に利用者が実測した lean system prompt 化（[[concepts/lean-prompt-rules-adaptation]]）の続きとして読める可能性がある（推論）。

## 公式プロンプティングガイド（未収集）

@hqmank は2026-10-05、Claude チームが Opus 5.5 を使いこなすためのガイドを書いたと紹介した。@hqmank の要約では、Opus 5.5 は自律的により長く作業し、毎回の返信の前に考えるので、プロンプトの書き方を変える必要がある。最初に試すべき tip として「タスク全体を1メッセージで渡し、何をもって完了（done）とするかを伝える」を挙げたところで投稿の捕捉が途切れており、残りの tip とガイド本体の URL は未収集である。

同じガイドについて @Voxyz_ai は、プロンプトから「think carefully（よく考えて）」を消すよう書かれていると紹介した。@Voxyz_ai の説明では、Opus 5.5 は毎回の返信の前に考え、どれだけ考えるかも自分で決めるため、この一文は不要になる。ガイドを Opus 5.5 自身に渡して、プロジェクト内の古いルールを見直させる使い方も勧めている（この引用投稿も途中で途切れている）。

「1メッセージで全体を渡し完了条件を言う」は、[[concepts/fable-5-prompting]] など自律性の高いモデル向けの指針に共通する方向と考えられる（推論）。

## 問い

- Claude チームのガイド本体を取り込み、@hqmank が挙げた tip の全体と、Opus 5 からの書き方の変更点を確認する
- Anthropic 公式の発表で、Opus 5.5 の発表日・価格・Opus 5 との性能差を確認する
- 投稿されたシステムプロンプトの全文を入手し、Opus 5 時代の lean system prompt から何が増減したかを比べる。自分の CLAUDE.md・rules と噛み合わなくなる箇所があるか

## 関連

- [[models/claude-opus-5]] — 前世代と考えられる Opus（2026-07-24発表）
- [[concepts/lean-prompt-rules-adaptation]] — Opus 5 移行でシステムプロンプトが変わり旧 rules が噛み合わなくなった実測記録。本モデルのシステムプロンプトを読むときの比較対象
- [[companies/anthropic]] — 開発元
- [[concepts/effort-level-selection]] — @trq212 が Opus 5.5 で同一タスクを effort 別に実行した実験（low 0/5 → high 4〜5/5 の事例など）
- [[concepts/html-output-format]] — 長いタスクを任せる前に HTML ダッシュボードを作らせる運用（@Voxyz_ai）
- [[concepts/opus-motion-design-studio]] — 公開直後に流行したコード描画動画の作り方を12ステップにまとめた講座（@0xMovez）。xhigh/max と画像読解による自己批評ループが要
- [[models/claude-haiku-5-5]] — @ClaudeDevs が「Opus 5.5 と組むサブエージェント」として勧めた小型モデル
- [[concepts/claude-code-long-task-harness]] — Opus 5.5 の長いタスクを完走させる Claude Code 7層ハーネス。発表時の 60% トークン削減は自分の計測で確かめよと注意（@beamnxw）
