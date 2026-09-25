# GitHub Self-hosted runner で回す24/365ソフトウェア工場

AIエージェントに止まらず開発させ続ける「ソフトウェア工場」を追い求めた結果として、@k1ito が行き着いた構成を4点にまとめた短文ポストである。@k1ito の結論は次の4つ。

- GitHub の **Self-hosted runner**（GitHub Actions のジョブを自前のマシンで実行する仕組み）の上で、時間制限なくエージェントを動かす
- 情報は基本的に **GitHub に集約** する
- こまめな **死活管理・タスク設計・仕様管理**
- ボトルネックになりがちな CI/CD を **Blacksmith**（GitHub Actions のジョブをより速いマシンで実行するランナーサービス）で高速化する

ポストには構成図と思われる画像が付いているが、画像の中身は取り込んでいない（未収集）。@k1ito は「みんなはどう？」と問いかけており、これは確立した手法でなく、個人の試行錯誤の暫定結論として書かれている。

実行基盤・情報置き場・CI をすべて GitHub に寄せている点が特徴だと考えられる。エージェント専用の管理基盤を別に建てる [[tools/grok-bot]] や [[tools/hermes-agent-overnight]] と比べると、Issue・PR・Actions という既存の開発の流れをそのまま工場の入出力に使う構成と読める。また「CI/CD がボトルネック」という指摘は、エージェントの生成速度が上がると律速がコードを書く工程から検証の待ち時間へ移る、という見方と整合する（wiki側の推論）。

## 問い

- Self-hosted runner のジョブ時間上限や、runner が落ちたときの死活管理を具体的にどう組んでいるか（添付画像・続報の取得）
- このvaultの launchd による定期 ingest を GitHub Actions + Self-hosted runner に移すと、何が得られて何を失うか
- CI の高速化はマシンの性能で解ける部分と、テスト設計（[[concepts/parallel-e2e-testing]]）で解く部分のどちらが大きいか

## 関連

- [[tools/grok-bot]] — クラウドエージェントを起動・監視・催促して PR まで運ぶ管理層の実例。死活管理を専用ボットに持たせる別解
- [[tools/hermes-agent-overnight]] — 夜間に自動化ワークフローを回す先行例。時間帯を区切る運用に対し、こちらは24/365の常時稼働を狙う
- [[concepts/loop-engineering]] — 人間がループの外に出てエージェントを回し続ける設計論。本ページはその実行基盤を GitHub に置いた個人実践
- [[tools/crabbox]] — エージェントの計算をリモートマシンへ逃がす基盤。対応プロバイダーに Blacksmith を含む
- [[concepts/software-factory-cost-equation]] — 「ソフトウェア工場」を全社規模のコスト方程式で扱う Uber の事例
