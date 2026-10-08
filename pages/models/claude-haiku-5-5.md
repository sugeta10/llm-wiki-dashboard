# Claude Haiku 5.5

[[companies/anthropic]] の小型モデル。@claudeai は2026-10-07に「これまでで最も安く、最も速く、最も高性能な小型モデル」として発表し、@ClaudeDevs は同日「Claude Platform と Claude Code で利用可能になった」と告知した。両アカウントとも、実行コストは平均で Claude Haiku 4.5 より約75%安いと述べている。

@ClaudeDevs は使いどころとして、[[models/claude-opus-5-5]] や Sonnet 5.5 のサブエージェントとして組み合わせること、要約・コンパクション（会話履歴の圧縮）・データベースクエリのような大量でコストに敏感なタスクを挙げた。上位モデルに判断を、Haiku に量の多い下請けを任せる分担は、[[concepts/advisor-executor-pattern]] や [[concepts/cost-effective-harness]] の「モデルを役割で使い分ける」設計と同じ方向と考えられる（推論）。

同じ @ClaudeDevs のスレッド内には、10万トークン未満のプロンプトは入力 $0.10／出力 $0.50（100万トークンあたり）・キャッシュ読み取り $0.01、10万トークン超は $0.50／$2.50・キャッシュ読み取り $0.05 という価格の投稿がある。本vaultが捕捉したのは Max/Team プランの API クレジット告知（[[business/claude-plan-api-credits]]）のリプライ先としてのこの1投稿だけで、投稿本文にはモデル名が書かれていない。スレッドの流れから Haiku 5.5 の価格と考えられるが、未確認である（推論）。

## 観察ログ（未検証）

- 2026-10-07: @claudeai／@ClaudeDevs「平均で Haiku 4.5 より約75%安く動く」。どのタスク構成での平均かは投稿に書かれていない

## 問い

- 公式発表本体で、ベンチマーク・価格表・コンテキスト長を確認し、スレッド内の価格投稿が Haiku 5.5 のものかを確かめる
- このリポの headless ingest（`claude -p`）の要約・分類系の下請けを Haiku 5.5 に回すと、品質を落とさずにコストが下がるか
- 「10万トークンで価格が段階的に変わる」体系なら、長文脈の要約タスクではどこで分割するのが得か

## 関連

- [[models/claude-opus-5-5]] — @ClaudeDevs がサブエージェントの組み合わせ相手として挙げた上位モデル
- [[concepts/advisor-executor-pattern]] — 高知能モデルと安価モデルを役割で組み合わせる連携パターン
- [[concepts/cost-effective-harness]] — 安価モデルへの委譲がいつ引き合うかの経済分析
- [[concepts/claude-code-compact-recovery]] — 用途に挙がったコンパクションの Claude Code 側の挙動
- [[business/claude-plan-api-credits]] — Max/Team プランの月次 API クレジット。Haiku 5.5 にも使える
- [[companies/anthropic]] — 開発元
