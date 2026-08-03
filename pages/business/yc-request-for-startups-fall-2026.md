# YC Request for Startups（2026年秋）

> **TL;DR**: Y Combinatorが2026年秋のRequest for Startups（RFS）で教育・国防・クラウド・消費者・crypto・物理世界データなど13分野の投資テーマを提示し、初めて米陸軍長官が自ら寄稿した。

YC自身はこの回のRFSを「AIが物理世界へ進出し始めている」という単一の軸で束ねていると位置づける。象徴が2つ目のテーマで、YCパートナーや創業者に混じって現職の米陸軍長官Daniel P. Driscollが直接寄稿している点にある（YC公式サイトのRFSページ、2026-08-02時点）。

## 13の投資テーマ

| テーマ | 寄稿者 | 要旨 |
|---|---|---|
| The Primer | Andrew Miklas | Neal Stephenson『ダイヤモンド・エイジ』の教育AI「プライマー」を範に、子どもの読み書き算数を家庭教師水準でアダプティブに教える消費者向けプロダクトを募る |
| The Future of American Defense | Daniel P. Driscoll（米陸軍長官） | 旧来の調達方式を否定し、低コスト迎撃機・次世代センサー・ドローン・強靭なロジスティクスなど、開放型システムに直接組み込める商用ハード/ソフトを募る |
| A Cloud for Small Software | Pete Koomen | エージェントが個人・小チーム向けに量産する「Small Software」向けの、AWS/Azureのような大規模向け複雑さを持たないデプロイ・共有基盤を募る |
| Multiplayer AI | Aaron Epstein | 個人が1人でチャットするAI利用を、Google DocsやFigmaのようにチーム全員が同じライブセッションに入って観察・介入・引き継ぎできる「マルチプレイヤー」なAIへ転換するプロダクトを募る |
| Compute at Sea | Francois Chaubard | データセンター用地・電力・許認可の逼迫を背景に、標準化モジュール船団で海上にコンピュートを分散配置する構想 |
| （見出しなし・寄稿者不明） | — | AIエージェントを「人として扱える」水準に達したことを根拠に、次の消費者向けプラットフォーム（GoogleやInstagramに匹敵する新規アイコン）をAIネイティブで狙う |
| （見出しなし・寄稿者不明） | — | 2030年に米国人の5人に1人が65歳超になる高齢化を背景に、音声対話・見守り・介助ロボティクス・家族向け介護コーディネーションを募る |
| （見出しなし・寄稿者不明） | — | 建設・保守・フリート運用など「机に座らない」80%の労働力向けに、AIエージェント・現場ロボット・ウェアラブル人間の3者を同時に管理する新しいオペレーティングシステムを募る |
| （見出しなし・寄稿者不明） | — | 弱気相場のcrypto市場を逆に好機と捉え、ステーブルコイン・資金調達・エージェント向け決済レールなど、非crypto企業も含め全YC企業が将来的にcryptoレールへ乗ることを見込む |
| Data for the Real World | Austin Tindle、Diana Hu | ロボットや自律気球でエネルギー・農業・物流・建設など物理産業の密なデータを集め、精密なモデル化と制御（気象操作級の応用まで）を可能にする企業を募る |
| （見出しなし・寄稿者不明） | — | ディープフェイク音声・映像による詐欺（$2500万の実被害事例を提示）を受け、プライバシーを犠牲にせず「相手が本物の人間である」ことを証明するインターネットの信頼レイヤーを募る |
| AI-Native Compliance Infrastructure | Daivik Goel | 州ごとの免許・監査・規制対応で分断された金融コンプライアンス業務を、モニタリング・異常検知・レポート生成までAIネイティブに再設計するインフラを募る |
| Self-Maintaining APIs | Harsha Gaddipati | APIプロバイダーの破壊的変更を人間が追いきれない問題に対し、変更をエージェントが顧客コードベースへ自動でPRとして適用する「Dependabot for APIs」的な仕組みを募る |

原文はMarkdown変換の過程で一部セクションの見出し（寄稿者名を含む）が欠落しており、該当箇所は本文から著者名を確認できなかった。憶測で補わず「寄稿者不明」とした。

いくつかのテーマは既存の投資テーゼと直接つながる。Multiplayer AIとA Cloud for Small Softwareは、[[business/startups-for-agents|「エージェント向けにスタートアップを作る」]]というテーゼの具体形で、どちらも「人間に売る」から「エージェントのハーネスに組み込まれる」への転換を前提にしている。Self-Maintaining APIsが描く「エージェントがコードベースへ直接PRを送る」運用は、[[business/self-improving-company|自己改善する会社]]が挙げた夜間自動MR生成の事例と同じパターンを、社外プロバイダー起点で一般化したものと考えられる。

## 観察ログ（未検証）

- 2026-08-02: Consumer AIのテーマで「AIエージェント運用コストは現在ユーザー1人あたり月$1,000程度だが、年10倍のペースで低下している」との予測（寄稿者不明・単一ソース）
- 2026-08-02: Self-Maintaining APIsのHarsha Gaddipati談「AWS在籍時、サービスダウンタイムの30%超が外部API/パッケージの変更見落としに起因した」という自己申告の実測値

## 問い

- 米陸軍長官の直接寄稿は、防衛スタートアップへの資金流入をどの程度加速させるか。次回以降のRFSでも国家機関からの寄稿が定着するか
- 「エージェント向けスタートアップ」というテーゼ（[[business/startups-for-agents]]）は、Multiplayer AIとA Cloud for Small Softwareのどちらの形で先に実現しそうか
- Self-Maintaining APIsの「Dependabot for APIs」は自分のwiki-ingest運用（外部ソースの構造変化への追従）にも応用できるか

## 関連

- [[business/startups-for-agents]] — 「エージェント向けスタートアップ」投資テーゼ。Multiplayer AI/Small Software Cloudの背景にある考え方
- [[business/self-improving-company]] — 同じYC発信。会社を自律ループの集合として再設計する構想とSelf-Maintaining APIsが同型
- [[business/founders-playbook]] — Anthropic公式のAIネイティブ起業プレイブック。RFSが示す「何を作るか」に対し「どう作るか」を扱う対
