# x-research-skills（Grokを「X検索専用レイヤー」にする構成）

> **TL;DR**: X のリアルタイム検索だけを xAI API の Grok に外注し、Claude Code / Codex からは Agent Skill 経由で呼ぶ構成。@HayattiQ がスクリプトを GitHub（`HayattiQ/x-research-skills`）で公開している。

> 📌 X bookmark: 19,871（2026-09-09 時点）

```mermaid
flowchart LR
  CC["Claude Code / Codex"] --> S["SKILL.md"]
  S --> SC["スクリプト（クエリ・フォーマット固定）"]
  SC --> API["xAI API（Grok）"]
  API --> X["X 投稿のリアルタイム検索"]
  X --> R["整形済みリサーチ結果"]
  R --> CC
```

出発点は、記事や投稿文のリサーチ全般はこなす [[tools/claude-code]] や ChatGPT が、「X の今」を追わせた途端に精度を落とすという観察である。@HayattiQ はこれを「能力の問題というより情報源の性質の問題」と切り分ける。つまりモデルを上位のものへ替えても解決せず、X のリアルタイム情報へ触る手段そのものを足す必要がある、という診断になっている。

足す手段として @HayattiQ が選ぶのが Grok である。X 社が作っているモデルであるため X 投稿の検索・要約に強く、これを API 経由で呼んで「検索専用マイクロサービス」として扱う。エージェント本体（Claude Code / Codex）は思考と文章生成に専念し、X からの取得だけを別プロセスに委ねる分業になる。@HayattiQ は所要時間を「エンジニアなら30分で動く」と見積もっている。

## 構成の3ステップ

1. **xAI API キーを取得する** — [x.ai](https://x.ai/) からサインアップして API キーを発行する。API は有料の従量課金のため、事前に課金しておく必要がある
2. **Grok を呼ぶスクリプトを書く** — Grok に渡すプロンプトを固定したスクリプトを用意する
3. **Agent Skill として登録する** — そのスクリプトを呼び出す `SKILL.md` を書き、Claude Code / Codex から起動できるようにする

## 肝は「何でも聞かない」こと

@HayattiQ が最も強調するのは、Grok に自由質問を投げるのではなく**検索クエリとフォーマットを固定する**点である。「AIトレンド 日本語 直近24時間」のように対象・言語・期間の制約を明示するとノイズが激減すると述べる。スキル側が担うのは「何を聞くか」ではなく「どう聞くかを毎回同じ形に揃えること」で、[[concepts/claude-skills]] の一般形（職務定義を持続させ毎回の文脈再構築を省く）をリサーチ工程に当てたものと言える。

なお、この構成は X 検索を Grok に寄せるため、X の公式 API を叩く [[tools/xurl]] とは前提が異なる。xurl は X Developer Portal でのアプリ登録と OAuth 認証が要る代わりに検索・投稿・メディアアップロードまで公式エンドポイントを直接扱えるのに対し、本構成は xAI の課金だけで済む代わりに取得内容が Grok の検索・要約を通した二次情報になる。どちらを採るかという同じ二択は、[[tools/hermes-agent-research-department]] の Scout エージェント設計でも「xurl スキルか Grok への切替か」として現れている。

## 観察ログ（未検証）

- 2026-02-10: xAI API の課金について「1回の呼び出しは0.1 USD くらい」（@HayattiQ）。記事本文はこの箇所で途切れており、クエリ内容による変動や上限は未確認
- 2026-02-10: 効果は「海外の盛り上がっていた投稿をうまく取得できている実感がある」という体感ベースの評価で、Claude Code / ChatGPT 単体との比較実測は示されていない

## 問い

- 自分の wiki の週次リサーチ（sources/twitter/ の供給）に、xurl による公式 API 取得と Grok 経由の検索を併用する意味はあるか。ブックマーク収集は xurl、話題の発見は Grok という役割分担が成り立つか
- 「検索クエリとフォーマットを固定する」効果は Grok 固有か、Web 検索を持つ他モデルでも同じ効きを持つか
- Grok の要約を経た二次情報を wiki に取り込む場合、Tier 判定（一次情報か否か）はどう扱うべきか

## 関連

- [[tools/xurl]] — 同じ「エージェントから X を触る」問題を X 公式 API 側で解く別解。開発者アプリ認証が要る代わりに一次データを直接取得できる
- [[tools/hermes-agent-research-department]] — リサーチ部門の Scout エージェントで「xurl か Grok か」という同じ二択を扱っている構成例
- [[concepts/claude-code-webfetch]] — Web 経由の取得が Haiku の事前要約を通る Claude Code 側の機構。「Web から X を調べると精度が落ちる」の一因を説明する
- [[concepts/x-algorithm-phoenix]] — X 本体のランキングも Grok ベースの Phoenix に移行済み。Grok が X 投稿に強いという主張の背景
- [[concepts/claude-skills]] — スクリプトを `SKILL.md` で束ねてエージェントに呼ばせる仕組みの一般形
