# claude-real-video

Claude Code に動画を「見せられる」ようにする OSS ツール（作者 HUANGCHIHHUNGLeo）。動画の URL かファイルを渡すと、変化のあった場面のフレームだけを選び、文字起こしを付けて、どの LLM でも読める形式に整えてくれる。処理は全部ローカルで動き、スキルとして組み込めば Claude Code が自分で動画を見て答えられるようになる、と紹介者の @commte は言う。

要は「動画 → 機械可読テキスト」の前処理器だ。全フレームを送るのでなく差分のあったフレームだけを抽出することでトークンを抑え、ネイティブに動画を扱えないモデルにも「見た内容」を渡せる中間形式に変換する。

## 問い

- 変化フレーム抽出＋文字起こしのローカル前処理は、このリポの YouTube Bridge（`notebooklm_youtube_bridge.py`）と何が違い、動画ソースの ingest にはどちらが向くか。
- 「どの LLM でも読める中間形式に整える」発想は、GUI でなく DSL を渡す[[concepts/intermediate-notation-pattern]]の動画版と言えるか。

## 関連

- [[tools/claude-computer-use]] — Claude がデモ録画をリプレイし UI を操作する隣接機能。「動画を機械可読にして扱う」点で重なる
- [[tools/claude-code]] — スキルとして組み込む先の本体
- [[concepts/intermediate-notation-pattern]] — 動画を LLM 可読な中間形式へ整える点で「中間記法を渡す」思想と接続
- [[concepts/claude-skills]] — スキルとして配布・組み込みできる形態
