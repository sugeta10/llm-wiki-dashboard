# agmsg

**agmsg** は、CLI AIエージェント同士（Claude Code・Codex・Gemini CLI等）が**共有SQLiteを介して直接メッセージを送り合える**OSSツール。Agent Skills（agentskills.io）ベースで実装されており、エージェント本体を改変せずスキルとして導入できる。依存は `bash` と `sqlite3` のみ。

作者: @fujibee（元ビジネス側の経営者、Claude CodeとCodexで毎日プロダクションコードを書く生活に戻った）

→ https://github.com/fujibee/agmsg

## 観察ログ（未検証）

- 2026-05-26: Claude Code（Opus 4.6）が思考ループにハマった場面でCodex（GPT-5.3）に同じ要件を渡すとほぼ一瞬で正しい出力が返った体験が開発動機
- 2026-05-26: Claude CodeとCodexで「役割分担」が自然に発生 — Claude Codeは日々の実装ドライバー（手数が多くテンポがいい）、Codexはレビューと難所の相談役（正しさに強い）
- 2026-05-26: monitor モードでは SQLite をリアルタイム購読し、メッセージが届いた瞬間に会話に割り込む。これにより2台のエージェントが人間の介入なしでマルバツゲームを完走した
- 2026-05-26: 設計上の最大の課題はClaude Code（Monitor/SessionStart hookあり）とCodex（これらなし）の機能差を吸収すること

## 検証済み事実

- 2026-05-26: 受信モードは手動取得・hook（Turn終了タイミング）・monitor（SQLiteブロッキング読み取り）の3方式。Codexのデフォルトはhook、Claude CodeのデフォルトはmonitorまたはbothモードまたはSQLite WALモード（マルチリーダー+1ライター）

## アーキテクチャ

| 方式 | 仕組み |
|-----|------|
| **手動取得** | `/agmsg` または自然言語で手動確認 |
| **hook** | Stopフックでターン終了時に自動チェック（Codexのデフォルト） |
| **monitor** | SQLiteをリアルタイム購読・メッセージ到着で即会話に割り込み（Claude Codeのデフォルト） |
| **both** | monitor主体 + hookを保険として併用 |

## チーム設定

同じ「チーム名」に参加したエージェントは互いにメッセージを送受信できる。初回起動時にチーム名とエージェント名を入力するだけ。

## 関連

- [[tools/claude-code]]
- [[tools/openai-codex]]
- [[concepts/multi-agent-patterns]]
- [[tools/openclaw]] — agmsgはagentskills.io（OpenClaw系スキル仕様）ベースで実装
