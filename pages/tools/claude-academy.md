# Claude Academy

> **TL;DR**: Anthropic が 2026-08-20 に公開した無料の公式学習サイト（academy.claude.com）。「製品ごとの使い方」と「AIそのものの基礎（AI Fluency）」の2本立てで、AIを触ったことがない人から日常的にClaudeを使う人まで同じ入口から入れる構成になっている。

> 📌 X bookmark: 27,399（2026-08-21 時点。like 24,074 / RT 2,638 / impression 4,308,644）

Anthropic の公式アカウント @claudeai は公開告知で「AIとは何かを探っている段階の人でも、すでに毎日Claudeを使っている人でも、その人がいる場所で出会える学習パスがある」と述べ、コースとチュートリアルはすべて無料で誰にでも開かれていると明言した。ここで注目すべきは、教材の構成が**製品カタログではなく学習パス**として切られている点である。5つの製品ごとに「何ができるか」ではなく「何を学ぶか」で入口が分かれ、その手前に製品非依存のAI基礎コース群が置かれている。ツールの使い方を覚えるだけでは委任の質が上がらない、という前提が設計に出ていると考えられる。

## 製品別の学習トラック

サイトの「Learn the Claude products」節では、5製品それぞれに1行の学習目標が添えられている（以下はサイト記載の転記）。

| トラック | 学ぶこと |
|---|---|
| Claude.ai | 問題を考え抜く・文書を起草する・データを分析することを、Claudeとの対話で行う |
| Claude Cowork | タスクを丸ごと渡し、仕上がった成果物を受け取る（自分用・チーム用の両方） |
| Claude Code | ターミナル・IDE・ブラウザでビルド／デバッグ／出荷する |
| Claude Tag | チームのチャンネルにClaudeをタグ付けし、全員が追える形で仕事を渡す |
| Claude Platform | API・Claude Console・MCP を使って自社プロダクトにClaudeを組み込む |

この5分割は [[business/founders-playbook]] に出てくる Anthropic 公式の Chat / Cowork / Code 製品マトリクスを、チーム向けの [[tools/claude-tag]] と開発者向けの Platform まで広げた形になっている。

## AI Fluency（製品非依存のAI基礎）

「Learn AI fundamentals with AI Fluency」節には AI Fluency というコレクションが置かれ、キャッシュ時点で以下の3本が掲載されている。

| 教材 | 形式 | 内容 |
|---|---|---|
| AI Fluency: Framework & Foundations | コース（14レッスン・クイズ1・4時間） | **4Dフレームワーク**（Delegation / Description / Discernment / Diligence）でAIと効果的・効率的・倫理的・安全に協働する |
| The 4 Properties of AI | チュートリアル（7分） | AIの4つの性質の概観。AIが得意な状況と限界のある状況を分ける要因のクイックリファレンス |
| AI Capabilities and Limitations | コース（13レッスン・クイズ1・3.5時間） | 次トークン予測・知識・ワーキングメモリ・ステアラビリティ・コンテキスト制限から、LLMにできること／できないことの正確なメンタルモデルを作る |

4Dの4語（委任・記述・見極め・入念さ）は、このwikiで扱ってきた委任設計の論点——何をどこまで渡すか（[[concepts/goal-loop-routine]]）、どう記述するか（[[concepts/claude-code-instruction-methods]]）、出力をどう検証するか（[[concepts/eval-loop]]）——を1つの語彙に畳んだものと読める。Anthropic自身が「AIリテラシー」を製品の使い方と分離して教材化した点が、この公開の実質だと考えられる。

なお、このwikiが持つサイトのキャッシュはトップページの冒頭部分のみで、上記より下のコース一覧は取得できていない。教材の総数・難易度体系は未確認。

## 問い

- 4Dフレームワーク（Delegation / Description / Discernment / Diligence）は、このwikiのingest→weekly生成ループのどの工程に対応するか。Discernment（見極め）が最も薄い工程はどこか
- 「AI Capabilities and Limitations」のワーキングメモリ／コンテキスト制限の説明は、実運用での圧縮・引き継ぎ設計（[[concepts/ai-session-handover]]）とどこまで一致するか
- Claude Platform トラックの教材は、[[tools/claude-mcp]] の設計判断（何をMCPサーバーにし、何をスキルにするか）まで踏み込んでいるか。踏み込んでいなければ、それは公式が「まだ定石がない」と見ている領域という可能性がある

## 関連

- [[companies/anthropic]] — 提供元。公式アカウント @claudeai が公開を告知
- [[tools/claude-code]] — 製品別トラックの1つ（ターミナル／IDE／ブラウザでの開発）
- [[tools/claude-tag]] — 製品別トラックの1つ（Slackチャンネル常駐でチームに仕事を渡す）
- [[tools/claude-mcp]] — Claude Platform トラックが扱う接続層（API・Console・MCP）
- [[business/founders-playbook]] — 同じくAnthropic公式の学習コンテンツ。製品マトリクス（Chat/Cowork/Code）が本サイトの製品別トラックと対応する
- [[business/gitlab-ai-fluent-teams]] — 導入企業側のAIフルエンシー育成プレイブック（GitLab）。「何を任せ・どう組み・どう見極めるか」という定義が本サイトの4Dとほぼ同じ分解で、自己診断のAI Literacy Ladderと連合型ガバナンスで組織に展開する
