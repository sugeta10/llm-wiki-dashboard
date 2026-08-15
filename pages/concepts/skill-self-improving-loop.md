# スキル自己改善ループ（会話履歴→Issue→Routines→triage→PR）

> **TL;DR**: スキルが「うまくいかなかったsignal」を自分の会話履歴から拾ってGitHub Issue化し、別の無人スキルがそれを判定・SKILL.md修正・PR作成まで回す3段構成。ふりかえり〜PR出しを自動化し、人間はマージ判断だけ残す。

[[concepts/self-refining-skills]]がスキル内の `LESSONS.md` に学びを追記する**スキル内蔵型**なのに対し、これはスキル本体とは別セッション・別スキルで改善を回す**外部ループ型**。発見（会話jsonlからのsignal抽出）と適用（SKILL.md修正＋PR）を分離するのが要点で、これにより対話前提の本体スキルの品質を落とさずに無人運転を実現できる。役割を3段に切る：

| 段 | やること | 場所 |
|---|---|---|
| 1. 発見 | 本体スキルの完了時に会話jsonlをサブエージェントに丸投げし「signal」を抽出→Issue起票 | ローカル |
| 2. 判定+適用 | 専用triageスキルがIssueをaccept/reject、acceptならSKILL.md修正＋PR作成 | Web（無人） |
| 3. 無人運転 | Routinesが2を1日1回起動 | Web（無人） |

**signalの定義**：ユーザーの修正指示（「違う」「stop doing X」）、同じ指示の繰り返し（スキルが内面化していないサイン）、進まないステップのループ、レビュースキルがSKILL.md文面をroot causeと名指しした箇所。生の会話jsonlはサイズが大きいのでメインコンテキストに乗せずサブエージェント側で処理する。

**なぜ本体と別スキルにするか**：本体スキルはユーザーとの対話前提なので無人運転に向かない。triage専用のローカルスキルを別建てし、本体の開発品質を保ったまま無人で回す。

**品質ゲート3段＋外側ループ**：完全自動の改善を見据え、`verify-diff`（Editがfinding意図を達成したかの実証検証・[[concepts/eval-loop]]のempirical発想）／`skill-review`（SKILL.mdのbest practices）／`publicity-review`（公開リポへのleak＝絶対パス・内部hostname・credential検出）の3段を直列で流す。各ゲートは内側ループを持ち、3段全体をさらに**最大3回外側ループ**させる。skill-reviewがSKILL.mdを編集するとverify-diffやpublicity-reviewの結果が変わりうるため、1回流して終わりでは整合が取れないのが理由。「どのゲートも1件もeditを入れないiterがあれば収束」「fatal verdictでbreak」で抜ける。

## 観察ログ（未検証）

- 2026-06-06: SonicGardenのプログラマ（hiroro-work）による実践報告。本体スキルは `dev-workflow`、triage側は `dev-workflow-triage`（[hiroro-work/claude-plugins](https://github.com/hiroro-work/claude-plugins)で公開）
- 2026-06-06: 数字は「自分1人だけが有効化した試験運用段階」のもの — 自動triageコミット40件以上/13日、自己ふりかえりIssue 21起票/21close/0open、1Issueあたりfinding 1〜4件
- 2026-06-06: 「自分がうまくいったと思ったときでも裏のサブエージェントとのやり取りで起きた問題を拾ってくれる」という主張（効果の一般性は未検証）
- 2026-06-06: Routinesで止めずに最後まで走らせる際のgotcha — ①サブスキルから戻った直後に待ち始める→判断ポイント直前に「止まるな」を再掲、②サブスキル出力が長文だと完結扱いされる→末尾を短いJSON（`{"status":...,"suggested_edits":[...]}`）で閉じる、③`.claude/`配下に作業ファイルを置くと許可ダイアログで止まる→`.triage/`等の外に置き`.gitignore`、④`allowed-tools`の書き漏れ（`TodoWrite`等）で停止、⑤Webデフォルトの `stop-hook-git-check.sh` が未コミット中間状態で誤発火

## 問い

- このwikiの `wiki-ingest`／`LESSONS.md` 運用は[[concepts/self-refining-skills]]型（内蔵）だが、外部triageループ型に移すと改善の質は上がるか。会話履歴からのsignal抽出を `retro` スキルが担えるか
- 「長文出力は完結扱いされるので短いJSONで閉じる」は headless 実行の一般則として効くか。自分のheadless ingestの出力形式に適用すべきか
- 品質ゲート3段を直列で外側ループさせる設計は、lint/review/ingestの整合検証にそのまま流用できるか

## 関連

- [[concepts/self-refining-skills]] — スキル内蔵型の自己改善（LESSONS.md追記）。本ページはそれを別セッション・別スキルに外部化した形
- [[concepts/eval-loop]] — 生成→採点→閾値未満で止めるループ。verify-diffの実証検証と品質ゲートの一般形
- [[concepts/recursive-self-improvement]] — AIがAI開発を加速する論考。本ページはその「スキル改善」版のミニチュア実装
- [[concepts/skill-building-best-practices]] — スキルの書き方ベストプラクティス（gotchas蓄積・on-demandフック）
- [[concepts/loop-engineering]] — L4ヒルクライミングループ（trace分析→ハーネス書き換え）の具体実装として位置づく
- [[concepts/llm-japanese-style-hooks]] — 指摘のたびにSkillがNGルールを登録し、週1で会話履歴を分析して繰り返し直している表現をまとめて登録する運用。本ページの会話履歴→ルール化ループを文章規範に適用した形（@yugen_matuni）
