# Codex エージェントループ

> **TL;DR**: [[tools/openai-codex]] の Codex CLI が「ユーザー入力→プロンプト構築→Responses API 推論→ツールコール or アシスタントメッセージ」を繰り返す中核ループ。完全ステートレス設計とプロンプトキャッシュ保護を両立させるため、`input` 配列を末尾追記のみで成長させるのが設計の肝。

このループが解くのは、完全ステートレス設計（`previous_response_id` を使わない）とプロンプトキャッシュのヒット率維持を同時に満たすという緊張関係である。ステートレスゆえ毎ターン会話全体を `input` 配列として送り直すが、その際プレフィックスが一致しないとキャッシュミスが起き、ツールリスト・モデル・サンドボックス設定・cwd の変動がそれを引き起こす。1ターンは1アシスタントメッセージで終了し、ユーザー・モデル・ツールの三者間インタラクションが Responses API 上で制御される。

**プロンプトの構成**。プロンプトのロール優先度は `system > developer > user > assistant` の降順。`instructions` フィールドが system/developer ロールを、`input` フィールドの各アイテムが会話履歴を担う。初回ターンの `input` は ①サンドボックス権限説明（developer ロール）→ ②developer_instructions（オプション）→ ③ユーザー指示（AGENTS.md 等の集約）→ ④環境コンテキスト（cwd・shell）→ ⑤ユーザーメッセージ の順で構成される。

**エンドポイントの分岐**。Responses API のエンドポイントは認証方式で分かれる：ChatGPT ログインは `chatgpt.com/backend-api/codex/responses`、APIキーは `api.openai.com/v1/responses`、ローカル（`--oss`）は `localhost:11434/v1/responses`。

**プロンプトキャッシュの保護**。ツールリストの順序変動・モデル切替・サンドボックス設定変更・カレントディレクトリ変更はキャッシュミスを引き起こす。そのため会話の途中でサンドボックスや cwd が変わっても、既存の `input` アイテムは書き換えず**新しいメッセージを末尾に追記**することでキャッシュプレフィックスを保護する。この末尾追記の不変則は [[concepts/prompt-caching]] の設計思想とも共通する。

**コンテキスト圧縮（Compaction）**。`auto_compact_limit` を超過すると `/responses/compact` エンドポイントを自動呼び出しし、返却される `type=compaction` アイテム（`encrypted_content` 付き）が以降の `input` を代替する。公式ブログは圧縮が "preserves the model's latent understanding"（モデルの潜在的理解を保持する）と記述している。

**reasoning と ZDR**。モデルが出力した `type=reasoning` アイテムは次の推論リクエストの `input` に含める必要がある。ZDR（Zero Data Retention）環境では `encrypted_content` がサーバー側で復号され、OpenAI は復号キーのみ保持しデータは保持しない。`previous_response_id` を使わない完全ステートレス設計により、プロバイダー側でキャッシュを保持する義務がなくなる点が ZDR 対応と整合する。これは [[concepts/12-factor-agents]] のステートレス設計原則と共鳴する。

## 観察ログ（未検証）

- 2026-05-21: **Compaction が劣化しにくい理由（@kenn の解釈）** — コンテキスト圧縮が劣化しにくいのは、テキスト形式ではなく**潜在空間のベクター表現でバイナリ圧縮**しているためという見方。元々膨大なテキストを超圧縮したものがLLMの重みであることと整合する設計、という解釈。公式記述の "preserves the model's latent understanding" をこのメカニズムの裏付けと読む（単一ソースの推論）
- 2026-01-23: `previous_response_id` を意図的に使わない設計判断は、ZDR 対応と完全ステートレス性のトレードオフを優先したものという解釈
- 2026-01-23: MCP ツールは Codex CLI のサンドボックス外で動作し、MCP サーバー側が自前でガードレールを実装する責任を負う（サンドボックス適用は Codex の shell ツールのみ、という著者の説明）
- 2026-01-23: コンテキストウィンドウの増大は会話ターン数に対して**二次オーダー**で成長するが、プロンプトキャッシュのヒット率が高ければ実質的に線形コストに抑えられるという見立て

## 検証済み事実

- 2026-01-23: **公式解説シリーズ第1弾** — Michael Bolin（OpenAI MTS）による Codex エージェントループの公式解説（2026-01-23）
- 2026-01-23: **プロンプトキャッシュミスの実例** — MCP サーバーのツール順不整合バグ（[PR #2611](https://github.com/openai/codex/pull/2611)）でツールリスト順序変動によるキャッシュミスが発覚した
- 2026-01-23: **Compaction の挙動変更** — 以前は `/compact` コマンドの手動実行のみだったが、`auto_compact_limit` 超過時に自動で `/responses/compact` を呼び出すようになった

## 問い

- 末尾追記によるキャッシュ保護は、自分の wiki ingest パイプライン（プロンプト前半固定・新規分のみ末尾追加）にもそのまま適用できるか
- @kenn の「潜在空間でバイナリ圧縮」という解釈は公式の実装説明と一致するか、それとも比喩か（一次ソースで検証したい）
- 二次オーダー成長とキャッシュ線形化の主張は、実際のトークン課金ログで確認できるか

## 関連

- [[tools/openai-codex]] — Codex CLIの概要・商用利用・Claude Codeとの比較
- [[concepts/prompt-caching]] — プロンプトキャッシュの仕組みとベストプラクティス（Claude版だがOpenAI版と設計思想が共通）
- [[concepts/multi-agent-patterns]] — エージェントアーキテクチャのパターン集
- [[concepts/12-factor-agents]] — 信頼性の高いエージェント設計原則（ステートレス設計原則と共鳴）
- [[concepts/claude-code-compact-recovery]] — Claude Code 側の compact（テキスト要約ベース・判断構造が落ちる）への対策パターン。潜在表現を保持する Codex の Compaction との対比
