# Jev Model Router｜Jev でリクエストごとに Claude Code のモデルを振り分ける Mod

@dani_avila7 が公開した、判断モデル [[models/jev]] に Claude Code のモデル選択を任せる Claude Code Mod（[[tools/claude-code-mods]] の仕組みで動く拡張）。Jev には TypeSafe（@typesafeai）の直接 API か、Vercel AI Gateway 経由でアクセスする。

@dani_avila7 の告知によると、Claude Code にリクエストを送るたびに Jev がサブエージェントのモデルを分類し、メインモデルはセッション開始時だけ分類する。メインモデルを途中で切り替えない理由は「〜を壊さないため」と書かれているが、告知文がそこで途切れており、何を壊さないためか（プロンプトキャッシュと考えられるが未確認）、分類の候補となるモデル・設定方法・リポジトリの所在は未収集である。告知には動画が添付されているが中身は未捕捉。

リクエストごとに「どのモデルに任せるか」を選ぶ処理は、生成を伴わない選択なので、Jev の「文章を生成せず選択肢に確率付きでスコアを付ける」性質とよく噛み合うと考えられる。[[concepts/decision-layer-model]] が描く「LLM が作り、Jev が決め、コードが実行する」分業を、Claude Code 自身のモデル配分に当てはめた例と読める。工程ごとに大・中・小のモデルを人が配分する [[concepts/llm-model-selection-strategy]] を、判断モデルが自動で行う形でもある。

## 問い

- 分類の候補モデルと判定基準は何か。リポジトリを取得して、Mod がどのイベント（`step` や `agent.spawn` など）にフックしているかを確かめる
- Jev の1判断あたりの遅延（mizchi の計測で約500ms）は、毎リクエストに挟んでも体感を損なわないか。Laya のようなローカルのクローン（[[tools/laya-mlx]]）に差し替えられるか

## 関連

- [[models/jev]] — モデル選択の判断を担う TypeSafe AI の選択型モデル
- [[tools/claude-code-mods]] — この Router が乗っている Claude Code の関数型拡張機構
- [[concepts/decision-layer-model]] — 生成 LLM から小さな分岐の判断を切り離す設計。モデル振り分けはその典型的な用途
- [[concepts/llm-model-selection-strategy]] — 工程ごとにモデルを人が配分する戦略。Router はこの配分を毎リクエスト自動化する
- [[tools/laya-mlx]] — Jev 型選択モデルのオープンウェイト版。ローカルで回せば判断の遅延を下げられる候補
