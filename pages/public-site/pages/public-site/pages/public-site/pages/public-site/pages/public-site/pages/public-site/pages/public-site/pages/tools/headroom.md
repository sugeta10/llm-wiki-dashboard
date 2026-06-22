# headroom

AIエージェントがLLMへ送るデータを**送信前に圧縮**してトークンを削減するライブラリ（[github.com/chopratejas/headroom](https://github.com/chopratejas/headroom)）。Claude Code でも利用できると紹介されている。

## 何を圧縮するか

エージェントがLLMに渡す各種コンテキストを対象とする：

- ツール出力
- ログ
- RAGチャンク
- ファイル
- 会話履歴

これらを送信前段で圧縮し、**トークンを60〜95%削減しつつ同等の回答精度を維持する**と主張されている。

## なぜ重要か

コンテキストウィンドウへ投入するデータ量がそのままコストと遅延に跳ね返るエージェント運用において、入力側の圧縮はモデル選択や [[concepts/prompt-caching]] とは別軸の最適化レバー。[[concepts/token-management]] が説く「タスクとモデルのミスマッチ検出」が*どのモデルを使うか*の最適化なら、headroomは*同じモデルへ何をどれだけ送るか*の最適化にあたる。

## 導入形態（4通り）

コードを変えずに既存スタックへ挿し込めるよう、複数の接続点を持つ。

- **ライブラリ** — `compress(messages)` を Python / TypeScript でインライン呼び出し
- **プロキシ** — `headroom proxy --port 8787`、コード変更ゼロ・任意の言語（OpenAI互換クライアントが通る）
- **エージェントwrap** — `headroom wrap claude|codex|cursor|aider|copilot` の1コマンド
- **MCPサーバー** — `headroom_compress` / `headroom_retrieve` / `headroom_stats` を任意のMCPクライアントに提供

ローカル実行（local-first）でデータが外部に出ない点を、ホスト型API圧縮サービス（Compresr等）やプロバイダ純正コンパクション（OpenAI Compaction）との差別化点として挙げている。

## 内部構成（コンテンツ種別ごとに圧縮器を切替）

`ContentRouter` がコンテンツ種別を検出し、最適な圧縮器へ振り分けるのが中核設計。

- **SmartCrusher** — JSON（配列・ネスト・混在型）の汎用圧縮
- **CodeCompressor** — AST認識。Python/JS/Go/Rust/Java/C++対応
- **Kompress-base** — agenticトレースで学習した自社HuggingFaceモデル（プローズ＝自然文の圧縮）
- **CacheAligner** — プレフィックスを安定化させ Anthropic/OpenAI の KVキャッシュをヒットさせる（[[concepts/prompt-caching]] と相補的）
- **CCR（reversible compression）** — 原文はローカルに保持し削除しない。LLMが必要時に `headroom_retrieve` で復元する可逆圧縮
- **Cross-agent memory** — Claude/Codex/Gemini 間で共有するメモリストア（出所付き・自動重複排除）

`headroom learn` は失敗セッションをマイニングし、修正を `CLAUDE.md` / `AGENTS.md` / `GEMINI.md` に書き戻す機能。これは [[concepts/self-refining-skills]] と同じ「失敗から学習ルールを蓄積する」発想を、ツール側で自動化したもの。

## 検証済み事実

- 2026-06-02: 公式READMEが提示する**自己公表ベンチマーク**（独立検証ではない）。実エージェント負荷での削減率: Code search 17,765→1,408トークン（92%）、SRE incident debugging 65,694→5,118（92%）、GitHub issue triage 54,174→14,761（73%）、Codebase exploration 78,502→41,254（47%）
- 2026-06-02: 精度保持ベンチ（N=100）: GSM8K 0.870→0.870（±0.000）、TruthfulQA 0.530→0.560（+0.030）、SQuAD v2 97%（19%圧縮時）、BFCL 97%（32%圧縮時）
- ライセンス Apache 2.0。Python 3.10+。シェル出力書き換えに [RTK](https://github.com/rtk-ai/rtk) バイナリを同梱

## 問い

- 「60〜95%削減・精度維持」は実タスクで再現するか。圧縮で欠落する情報が回答品質に効くケースはどこか
- このwikiのingest/Query作業（ファイル全文読み込み）に組み込めるか。[[concepts/prompt-caching]] と併用したときの効果の重複・相乗はどうか

## 関連

- [[concepts/token-management]] — トークン消費を経営イシューとして可視化・最適化する考え方
- [[concepts/prompt-caching]] — Claude APIのプロンプトキャッシング（送信データ再利用によるコスト削減）。CacheAlignerが狙うKVキャッシュヒットと同じレバー
- [[concepts/self-refining-skills]] — `headroom learn` が CLAUDE.md/AGENTS.md に修正を書き戻す失敗学習ループの発想
