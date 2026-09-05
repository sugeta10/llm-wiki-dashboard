# LLM Wiki

[[people/andrej-karpathy]] が提案した、LLM が継続的にメンテナンスする Markdown ベースの知識ベース。ベクトル DB を使わず、Markdown ファイルだけで運用する。

## 核心概念

- **ソース分離**: `sources/` に生データを不変で保存、`wiki/` に LLM が生成した知識ページを蓄積
- **3操作**: ingest（取り込み）・query（問い合わせ）・lint（メンテナンス）
- **自動成長**: 日報や URL を ingest するたびにプロジェクト・人物・技術ページが厚みを増す
- **Obsidian 互換**: `[[ページ名]]` リンク形式でグラフビュー可視化が可能

## 実装パターン（Claude Code × Routines）

[[tools/claude-code]] の Routines 機能で完全自動化が可能：

```
外部ツール（Slack / GitHub / Calendar 等）
  ↓ MCP コネクタ
Claude Code Routines（定時実行）
  ↓ 日報生成 → sources/ に保存
ingest → wiki/pages/ に知識蓄積
  ↓
Obsidian でグラフ可視化・検索
```

## CLAUDE.md の役割

wiki の操作ルール（ingest/query/lint の定義）を記述する設計図。Claude Code はこれを読み込み Wiki メンテナーとして動作する。

## 活用例

- 月曜朝のタスク確認: 「先週の進捗と今週やるべきことを整理して」
- プロジェクト経緯検索: 「タスクフォースXの決定事項を時系列で」
- 月次振り返り・1on1準備・スキル棚卸し

## エンティティページ設計

事前設計不要。`CLAUDE.md` に大まかな指針を書けば、Claude が日報の内容を見て必要なページを自律的に作成・更新する。

## 毎日自動成長するObsidian Vault

「What you do nothing」モデル: launchd / cron で `claude -p` を定時起動し、sources/ に積み上がったコンテンツを自動 ingest する。人間の介入なしに毎日 wiki/ が厚くなる構成。Obsidian のグラフビューでナレッジネットワークが可視化される。

スキルの自己改善ループ([[concepts/self-refining-skills]])を組み合わせると、ingest 品質も自動向上する。

## Claude × Obsidian 実装事例（国内）

日本語圏で Claude Code × Obsidian の組み合わせが急速に普及：

- **「勝手に育つ第2の脳」**: launchd で `claude -p` を定時起動 → sources/ に自動蓄積 → wiki/ が毎日成長（[[tools/claude-code]] Routines と同等の効果）
- **「5分で作るAI第二の脳」**: Obsidian vault に CLAUDE.md を置くだけで即座に LLM Wiki として機能する最短構成
- **「腐らせない方法」**: 週1回の lint + 月1回の全体見直しサイクルで知識ベースの鮮度を保つ
- **「完全解説（決定版）」**: `sources/` 不変・`wiki/` LLM管理・`[[ページ名]]` リンクグラフの3原則を詳解

## OpenAI創設メンバー視点のPKM

OpenAI創設メンバーが提唱した Obsidian 活用術（41,000人ブックマーク）：
- **第2の脳**の本質は「思考の外部化」ではなく「思考のネットワーク化」
- Atomic Notes（原子的メモ）: 1ノート1トピックで再利用性を最大化
- LLM Wiki との親和性が高い（ページが小さいほど LLM の編集精度が上がる）

## フォルダ構造の実装（NickSpisak 版）

@NickSpisak_ による Karpathy 式 Second Brain の具体的実装（41,000件ブックマーク）：

- **3フォルダ**: `raw/`（生データ投入）・`wiki/`（AI整理済み）・`outputs/`（質問への回答）
- **CLAUDE.md（AGENTS.md）**: LLMへの指示書。「何のナレッジベースか・どう整理するか」を記述
- **編集はAIの仕事**: wiki/ を手動編集しない。AIが読み・問い・更新する
- **月次ヘルスチェック**: 矛盾・未説明のトピック・根拠なし主張を定期検出

> 「Obsidianは必要ない。フラットなMarkdownフォルダ + スキーマファイルが本質」（Karpathy）

Obsidianは有効な選択肢だが、47個のプラグインで設定に時間をかけるよりフラットファイルが9割の用途で勝る。

## PKM × エージェント統合の視点

Obsidianをエージェントのコンテキスト置き場として「消費」するだけでは資産にならない。

**3つの統合**（@pkm_tk111）：
1. **自分のノート** — 自分の言葉で書いたメモ（思考の蓄積）
2. **外部ノート** — 参照情報・コンテキスト
3. **エージェント** — AIの実行力

自分の言葉でメモを書くことで Obsidian リンクグラフが構築され、エージェントへのコンテキスト渡し精度が上がる。[[tools/claude-harness]] のスキル設計もObsidian蓄積から構築すると質が高まる。

## RAGとの比較

| 項目 | RAG | LLM Knowledge Base |
|------|-----|--------------------|
| 知識の形式 | クエリごとに断片を検索 | 事前にコンパイルしたMarkdown wiki |
| 永続性 | クエリごとに再構成 | wiki が使うたびに成長 |
| 規模感 | 大規模文書に強い | ~100記事・~40万語規模が最適 |
| 人間の役割 | クエリを書く | キュレーションと方向づけ |

Karpathy氏: 「高度なRAGに手を伸ばす必要があると思ったが、LLMはindex ファイルとサマリーの自動維持がかなり上手で、関連データもわりと簡単に読んでくれた」— RAGか wiki かは二択ではなく、アドホックな質問にはRAG、全体像把握には wiki という使い分けも有効。

## 国内実践者による実装例（classmethod版）

クラスメソッドの森茂氏による実装：
- `workspace/knowledge/` が Raw sources、各ディレクトリの `CLAUDE.md` が Schema、`workspace/wiki/` が Compiled Wiki
- **Memory MCP**（Mem0 + pgvector）をRAG的検索レイヤーとして追加し、wiki と検索の両方を使い分け
- `/kb-compile` カスタムコマンドで `--all`（全体更新）/ `--lint`（矛盾検出）を実行
- `_index.md` に30プロジェクトの全体地図が生成され、新セッション開始時の認知負荷を大幅削減

> Karpathy: 「hacky なスクリプトの寄せ集めではなく、ここには素晴らしいプロダクトが生まれる余地がある」

## 運用1ヶ月の知見：要約より「繋げる力」（tsurubee）

LLM Wiki を1ヶ月運用した tsurubee は、LLM の真価は要約ではなく**別々のタイミングで読んだソースを繋げる力**にあると結論づけている。要約だけなら NotebookLM や ChatGPT のファイルアップロードで足りる。差を生むのは、複数ソースを横断して共通構造・分類・反例を抽出する**概念ページ**が、ingest のたびに LLM の手で自動更新され育っていく点である。

なぜ人間がこれをできないかというと、退屈な部分が読むことでも考えることでもなく **bookkeeping**（クロスリファレンス更新・要約の最新化・新データと旧主張の矛盾記録・数十ページの一貫性維持）だからで、3本までは続いても10本50本で維持コストが価値を上回り更新を諦める。LLM は飽きず忘れず1パスで多数ファイルを書き換えるため維持コストがほぼゼロに落ち、wiki が "persistent, compounding artifact"（持続的に蓄積する成果物）として維持され続ける。tsurubee は ingest スキルに「横断的知見」（複数ソースを並べて初めて見える観察）と「未解決の問い」（次に調べるべき問い）のセクションを必須化しており、**この箇条書きが時間とともに育つことが LLM Wiki の本体**だとする。

### 「繋げる力」の2類型（tsurubee の実例）

- **多数の同種ソースの統合**（事例1）：Automated Scientific Discovery の論文を10本以上 ingest した結果、概念ページが(1)各論文に散らばる評価軸を「実行ベース／人間ピアレビュー／LLM-as-Judge／実wet-lab検証」の4カテゴリに整理し、(2) 別々の3論文の独立観察（自動レビューLLMは人間より2.3点甘い／人間執筆論文の9割をreject／評価LLMを変えると比較レートが約68点変動）を「LLM-as-Judge は誰が書いたか・どのLLMが評価者かに強く依存し中立な評価軸として扱えない」という1パターンの3観測面に統合した
- **少数の異なる視点の対比抽出**（事例2）：Sam Altman と Dario Amodei のエッセイ計4本を別々に ingest しただけで、`agi` の概念ページに2人のAGI定義の対比（Altman=AGI→superintelligenceという方向性の語彙／Amodei=powerful AIの検証可能な5機能要件）が自動で立ち上がった。比較を意図せず投入したのに wiki 内で勝手に対立軸が再構成された

いずれも、LLM が新ソースを ingest するたびに過去ページを読み返して整合性を取り直すから成立する。人間が3ヶ月前のノートに戻って書き換える行為はほぼ発生しない。

### 課題：繋げた先に「理解」のボトルネックがある

tsurubee が運用で感じた根本的な限界。LLM が概念ページを綺麗に整理しても、**人間が読んで自分の理解として消化しないと「自分の知識」にはならない**。Karpathy 流の「人間はほとんど直接編集しない」設計を徹底すると、人間側のドメイン理解が深まらない懸念がある。対策として tsurubee は、(1) ingest するソースを**自分の手で選ぶ**（arXiv 新着の自動 ingest はしない。勝手に育った概念ページは「いつの間にこんなページが」状態になり理解ボトルネックを悪化させる。量より質でまず wiki を溢れさせない）、(2) wiki を辞書的に消費するだけでなく概念ページを能動的に読み返して自分の理解として再構成する、の2点を挙げる。習慣化の方法は本人もまだ未解決としている。

## 「既存ページまで更新される」を差分で実測した検証（NTT DATA TECH）

NTT DATA TECH は AWS Samples の [[tools/knowledge-acquisition-skill]] を使い、Amazon Bedrock AgentCore の3資料だけで初期 Wiki を作ってから資料を1本だけ追加する Before / After 検証を行っている。結果は、新規内容ページ2件に加えて**既存ファイル7件（内容ページ5件と index / log）が更新**され、内容ページ群の wikilink 出現回数が159回から207回へ増えた。さらに Before と一字一句同じ質問への回答構造が「Runtime 中心」から「harness を入口とした構成」へ変化した。モデル固定・別セッション・Web検索禁止で条件を揃えた上での観測だが、Before / After とも各1回（N=1）である。

この検証が示すのは、LLM Wiki の中核主張である「新資料が既存知識を再編集する」が、ファイル差分という検証可能な形で観測できるということである。同時に NTT DATA TECH は、生成された記述に原典の要約だけでなく LLM による再構成が混ざる点を挙げ、実運用では Git 差分レビューと重要事項の原典照合を前提にすべきだとしている。

## 派生プロジェクト:「LLM Wiki v2」（未検証）

@heynavtoor は、Karpathy の LLM Wiki が公開48時間で反響を呼んだ後、それを拡張した「LLM Wiki v2」という派生プロジェクトが登場したと投稿している。挙げられている追加機能はメモリライフサイクル管理・確信度スコアリング・知識グラフ・自動フック・忘却曲線の5つ。ただし投稿には実装者名やリポジトリへのリンクが含まれておらず、機能語の実体（設計・コード）は本ソースだけでは確認できない。

その後 KiKi@AIx個人開発が、この投稿と Rohit Ghumare 名義の Gist を日本語で要約している。要約によれば v2 は7機能（確信度スコア・4層メモリ階層・型付きナレッジグラフ・ハイブリッド検索・自動フック・忘却曲線・矛盾解決）と6レベルの導入スペクトラムからなるアーキテクチャの青写真で、コード実装は含まない。詳細は [[concepts/llm-wiki-v2]] に切り出した。

## 問い

- このwikiは launchd で自動 ingest している。tsurubee の「手動でソースを選ばないと理解ボトルネックが悪化する」という指摘と、自動 ingest（量）をどう両立させるか
- 「横断的知見」「未解決の問い」の必須化は、このwikiの長文2セクション構造（本文＋観察ログ）や各ページの `## 問い` と機能的に同じか。概念ページが「繋がり」を実際に育てる構造になっているか点検する
- 繋げてもらった概念ページを能動的に読み返して理解に落とす運用（[[concepts/output-first-learning]] や weekly insights 等）を、どう習慣に組み込むか

## 関連

- [[people/andrej-karpathy]]（提唱者）
- [[tools/claude-code]]（実装プラットフォーム）
- [[tools/claude-harness]]
- [[concepts/agentic-coding]]
- [[concepts/vibe-coding]]
- [[concepts/self-refining-skills]]
- [[concepts/agent-memory-layer]]（知識ベースは「到達点の保存」に偏り、捨てた推論経路を取りこぼすという指摘）
- [[concepts/open-knowledge-format]] — このMarkdownベース知識ベースのパターンをベンダー中立な標準フォーマットに昇格させたGoogle Cloud発の仕様
- [[concepts/output-first-learning]] — 「理解のボトルネック」対策と通じる、読んだものを自分の理解に再構成する血肉化法
- [[tools/notebooklm]] — 要約特化の代替。tsurubeeは「サマリーだけなら NotebookLM で足りる、差は概念ページの繋げる力」とする
- [[concepts/second-brain-operations]] — 本思想を「維持ループのモデルティア振り分け・skeptic付きリサーチ・pay-per-read」まで含むフル運用設計に落とした実装ガイド（@EXM7777）
- [[tools/hermes-agent-personal-vault]] — 「メモリは索引、知識はファイルに」という本思想をHermes Agent向け6プロンプトに具体化した個人生活ナレッジベース構築ガイド
- [[tools/cloudflare-os]] — GitHub正本のMarkdown知識ベースをクラウド側エージェントにも参照させた実践例と、その月額コストの実測（Markdown約400件で実質0円）
- [[concepts/llm-wiki-vs-company-brain]] — 個人・小チーム向けのKarpathy式と企業向けCompany Brainの分岐点を権限・鮮度等の5点で整理し、4つの質問による判定基準を提示
- [[concepts/evergreen-notes-claude-md]] — 同じMarkdown＋ウィキリンクの型を、ingest/query/lintの3操作でなく「回答直後にノート化を提案させる」会話起点の成長で回す実装（tokuhirom / 64p.org）
- [[tools/knowledge-acquisition-skill]] — AWS Samples の実装。「資料1本追加で既存ページ7件が更新され回答構造も変わる」をファイル差分で実測した検証例
- [[concepts/self-owned-ai-memory]] — AIとの会話記憶を Obsidian の日記と git 保存に置く個人の自作長期記憶（@zapabob_ouj）。Markdown とリポジトリを器にする本思想と同じ基盤に立ち、溜める対象が「読んだソース」でなく「会話と思考」である点が違う
- [[concepts/llm-wiki-v2]] — 本パターンの弱点（全知識を永遠に等しく有効と扱う・200ページ超で index.md 検索が破綻・矛盾を検出しない）を確信度の減衰・メモリ階層・型付きグラフ・ハイブリッド検索・矛盾解決で補強する派生青写真
