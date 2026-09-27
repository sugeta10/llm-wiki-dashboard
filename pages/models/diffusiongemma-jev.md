# DiffusionGemma-Jev（djev）

Google の Gemma 公式アカウント（@googlegemma）が2026-09-22に紹介したモデルで、略称は djev。発表の中心はデプロイの手軽さで、Google Cloud Run 上に **Jev API 互換のエンドポイント**を1コマンドで立てられるようになった、と @googlegemma が述べる。

@googlegemma が示す性能は、1ステップのレイテンシが約35〜60ms、32件まとめたバッチ処理で毎秒約100〜123リクエストである。ポスト本文は「It's a straightforward way to」で途切れ、添付動画の中身も未捕捉のため、モデルの構造・サイズ・ライセンス・精度は未収集である。

「Jev API 互換」とあることから、TypeSafe の [[models/jev]] と同じく「文章を生成せず、渡した選択肢に確率やスコアを返す」判断モデルの呼び出し口を持つと考えられる（推論）。そうであれば、Jev 本体を呼んでいるエージェントや [[concepts/decision-layer-model]] の構成を、呼び先を自前の Cloud Run に替えるだけで移せることになる。名前の「Diffusion」が拡散モデル型の生成を指すのかも、ポストからは読み取れない。1ステップ35〜60msという値は、mizchi が測った Jev 本体の1リクエスト約500ms（日本から）と手元 MLX の Laya の8〜9ms（[[tools/laya-mlx]]）の中間にあたる。ただし測定条件が異なるので単純比較はできない。

## 問い

- Jev 本体と同じ問い（Choice/Score/Noul）を投げたとき、答えの一致率と確信度の出方はどれくらい違うか
- 「Diffusion」は何を指すのか。拡散型の言語モデルを判断タスクに使っているのか
- 自前の Cloud Run に置く利点（データを外に出さない・レート制限が無い）は、Jev の入力100万トークン0.042ドルという価格と比べて運用コストに見合うか

## 関連

- [[models/jev]] — 本モデルが API 互換を掲げる元の判断モデル（TypeSafe）
- [[tools/laya-mlx]] — 同じく Jev 型の判断を手元で回すオープンウェイトの選択モデル。ローカル実行側の選択肢
- [[concepts/decision-layer-model]] — Jev 型モデルを判断レイヤーに置く設計。呼び先の差し替え候補として本モデルが入る
- [[companies/google]] — Gemma の開発元
