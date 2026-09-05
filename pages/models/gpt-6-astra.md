# GPT-6 Astra

OpenAI の新世代モデル。本 vault で最初に現れたのは、OpenAI の Codex DX（開発者体験）担当 Eric Provencher（@pvncher）が 2026-09-04 に公開した記事「Rethinking skills and prompts for GPT-6 Astra」で、Codex のスキル・AGENTS.md・タスクプロンプトをこのモデル前提で書き直すよう促す内容だった（[[concepts/gpt-6-astra-skills-prompting]]）。発表本体・価格・ベンチマーク・提供条件は未収集。

Provencher の記事から分かるのは次の点に限られる。

- かつて多くの手取り足取りと足場を要した作業がもう要らなくなっている世代であり、GPT-6 Astra では蓄積した指示の見直しがこれまで以上に重要になる、と Provencher は述べる
- GPT-6 Astra は、変更のたびにプロジェクト全体を見直すよう押されなくても何を読む必要があるか自分で見つけられる、と Provencher は述べる
- Sol や Luna（[[models/gpt-5-6]] の能力ティア）に効く指針が GPT-6 Astra を過剰に縛りうる、と Provencher は述べており、Codex チームは Astra を Sol／Luna と並ぶ別のモデルとして扱っている

命名について。[[models/gpt-5-6]] では「数字が世代、Sol・Terra・Luna が永続的な能力ティア」と OpenAI が説明していたが、「GPT-6 Astra」は新しい世代番号と新しい名前の組み合わせにあたる。Astra が Sol と同じ意味でのティア名なのか別枠なのかは、記事からは判断できない。

## 問い

- OpenAI 公式の発表ページ（openai.com/index/gpt-6-astra）を ingest し、価格・ベンチマーク・Sol／Terra／Luna との関係を埋める
- Codex の既定モデルは GPT-6 Astra に切り替わったか（[[tools/openai-codex]] のモデル構成節は 2026-07 時点）
- 同時期（2026-09-01）に発表された Anthropic 側の [[models/claude-fable-5-1]] との比較が、[[models/gpt-5-6]] のときのように OpenAI 側の発表で名指しされているか

## 関連

- [[models/gpt-5-6]] — 前世代（Sol／Terra／Luna）。本モデルはその次の世代番号
- [[companies/openai]] — 開発元
- [[tools/openai-codex]] — 本モデルを前提にスキル指針が更新された製品
- [[concepts/gpt-6-astra-skills-prompting]] — 本モデル向けにスキル・AGENTS.md を見直す Codex チームの指針
- [[models/claude-fable-5-1]] — 同時期に発表された Anthropic 側の最新版
