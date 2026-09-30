# Dots（OpenAI）

OpenAI が DevDay（2026-09-29 頃）で発表した、クラウド上でバックグラウンド作業するボットの製品。本 vault での初出は @umiyuki_ai による DevDay 発表まとめのポストで、@umiyuki_ai は「要するに OpenAI 版の GrokBot」と要約している。公式発表本体・料金体系・対応ツールは未収集。

@umiyuki_ai が伝えている仕様は次の 3 点に限られる。

- 複数のボットを作れ、それぞれがクラウド上でバックグラウンドで作業する
- Pro プランで使える
- チャットの利用枠は Codex の容量とは別枠

比較対象の [[tools/grok-bot]] は、SpaceXAI の @lingxi が領域別ボット 5 体と運営ボット 1 体で 200 超のクラウドエージェントを管理する運用を書いた製品である。「OpenAI 版の GrokBot」という要約は、ボットを複数立てて手元の PC を離れても作業を続けさせる形が共通だという意味と考えられる。Grok Bot が Cursor のクラウドエージェントを束ねる管理役なのに対し、Dots が他のエージェントを管理するのか自ら作業するのかはポストからは読み取れない。

同じ DevDay では新モデル [[models/gpt-6-1-sol]] も発表されたと @umiyuki_ai は伝えている。

## 問い

- Dots はコーディング専用か汎用の作業ボットか。[[tools/openai-codex]] のクラウド実行との役割分担はどうなっているか
- 「チャットは Codex 容量と別枠」は、Codex を使い切っても Dots とのチャットは続けられるという意味か。公式の料金ページで確かめる
- 夜間に定期作業を回す [[tools/hermes-agent-overnight]] や [[concepts/github-runner-agent-factory]] の運用を Dots で置き換えられるか

## 関連

- [[tools/grok-bot]] — @umiyuki_ai が比較に挙げた先行製品
- [[tools/openai-codex]] — 利用枠が別扱いとされる OpenAI のコーディングエージェント
- [[companies/openai]] — 開発元
- [[models/gpt-6-1-sol]] — 同じ DevDay で発表されたモデル
