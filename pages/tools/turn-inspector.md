# Turn Inspector｜Claude Code のコンテキストウィンドウをターンごとに覗く Mod

@dani_avila7 が公開した Claude Code Mod（[[tools/claude-code-mods]] の仕組みで動く拡張）で、各ターンで Claude のコンテキストウィンドウに何が入っているかを確かめられる。@dani_avila7 の告知によると、表示対象はシステムプロンプト・ツール・メモリ・過去のターン・モデル出力・ツール結果の6種で、ターンを追うごとにコンテキストウィンドウが埋まっていく様子を見られる。告知文は短縮URLの手前で途切れており、インストール方法・リポジトリの所在・どのイベントにフックしているかは未収集。添付動画の中身も未捕捉。

[[concepts/context-engineering]] が論じる「何をコンテキストに置くか」の設計は、実際に何が置かれたかを見られないと効果を確かめにくい。このMod はその観測側を担う道具と考えられる（wiki 側の推論）。たとえば CLAUDE.md やスキルを削ったとき、システムプロンプトやメモリの占める量が本当に減ったかをターン単位で確認する用途が想定できる。Mods の `$.session.usage`（コンテキスト使用率など）や `step`（モデルへの1リクエスト）イベントを使って実装されている可能性があるが、未確認である。

## 問い

- リポジトリを取得し、6種の内訳をトークン数で出すのか、中身の全文を出すのかを確かめる
- このwikiの CLAUDE.md・スキル・メモリが1ターン目にどれだけの割合を占めているかを、このMod で実測できるか

## 関連

- [[tools/claude-code-mods]] — この Mod が乗っている Claude Code の関数型拡張機構
- [[tools/jev-model-router]] — 同じ @dani_avila7 による別の Claude Code Mod
- [[concepts/context-engineering]] — コンテキストに何を置くかの設計論。本 Mod はその結果を観測する側
- [[concepts/claude-code-context-hierarchy]] — Memory・Slash commands などコンテキストの層構造。Turn Inspector の表示区分と照らし合わせられる
