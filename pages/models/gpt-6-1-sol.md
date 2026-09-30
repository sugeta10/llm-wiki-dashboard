# GPT-6.1 Sol

OpenAI が DevDay（2026-09-29 頃）で発表したモデル。本 vault での初出は @umiyuki_ai による DevDay 発表まとめのポストで、@umiyuki_ai は「Astra 並みの性能を 1/5 の価格で実現」と一言で紹介している。発表本体・価格の実数・ベンチマーク・提供条件は未収集。

名前の読み方について。[[models/gpt-5-6]] で OpenAI が説明した「数字が世代、Sol・Terra・Luna が能力ティア」という命名に従うなら、GPT-6.1 Sol は 6 系世代の Sol ティアにあたると考えられる。比較対象として挙がった [[models/gpt-6-astra]] は、Codex チームが Sol／Luna と別のモデルとして扱っていた上位モデルで、今回の紹介は「上位モデルの性能を Sol ティアの価格帯に降ろした」という位置づけと読める（いずれも wiki 側の推論で、公式の説明は未確認）。

同じ DevDay では、クラウド上で複数のボットがバックグラウンド作業する [[tools/openai-dots]] も発表されたと @umiyuki_ai は伝えている。

## 問い

- 「Astra 並み・1/5 の価格」は何のベンチマーク・どの価格同士の比較か。公式発表を ingest して実数を埋める
- Codex の既定モデルや [[tools/openai-dots]] の実行モデルは GPT-6.1 Sol になったか
- Sol ティアに寄せた指針と Astra 向けの「縛りすぎない」指針（[[concepts/gpt-6-astra-skills-prompting]]）のどちらが GPT-6.1 Sol に合うか

## 関連

- [[models/gpt-6-astra]] — 比較対象として名指しされた上位モデル
- [[models/gpt-5-6]] — Sol／Terra／Luna ティア命名の出どころ（前世代の Sol は $5/$30）
- [[companies/openai]] — 開発元
- [[tools/openai-dots]] — 同じ DevDay で発表されたバックグラウンド作業ボット
