# CodeGraph

AIコーディングエージェント（[[tools/claude-code]]・[[tools/cursor]]・[[tools/openai-codex]]・[[tools/hermes-agent]]）向けの**セマンティックコード知識グラフ**ツール。コードベースを事前インデックス化してSQLiteに保存し、エージェントがgrep/Read探索を省いてグラフに直接クエリできるようにする。**100%ローカル動作・APIキー不要**。

関連: [[tools/claude-code]] / [[tools/cursor]] / [[tools/openai-codex]] / [[tools/hermes-agent]]

## 検証済み事実

- 2026-05-24: **平均コスト35%削減・トークン57%削減・速度46%向上・ツール呼び出し71%削減**（7つのOSSリポジトリ・Claude Opus 4.7ヘッドレスで4回実行中央値）
- 2026-05-24: ベンチマーク対象: VS Code（TS・約1万ファイル）・Excalidraw・Django・Tokio・OkHttp・Gin・Alamofire の7コードベース
- 2026-05-24: Tokioでの結果: $0.42 vs $2.41（WITH/WITHOUT）、4回呼び出し vs 53回呼び出し
- 2026-05-24: 19言語以上対応: TypeScript・JavaScript・Python・Go・Rust・Java・C#・PHP・Ruby・C・C++・Swift・Kotlin・Dart・Lua・Luau・Svelte等
- 2026-05-24: Webフレームワーク14種のルーティング認識: Django・Flask・FastAPI・Express・NestJS・Laravel・Rails・Spring・Gin・Axum・actix・ASP.NET・React Router・SvelteKit等
- 2026-05-24: MCPサーバーとして動作。提供ツール: `codegraph_context`・`codegraph_trace`・`codegraph_explore`・`codegraph_search`・`codegraph_callers`・`codegraph_callees`・`codegraph_impact`・`codegraph_node`
- 2026-05-24: ファイルウォッチャー（FSEvents/inotify/ReadDirectoryChangesW）でグラフをリアルタイム同期。設定不要

## 観察ログ（未検証）

- 2026-05-24: 著者による説明: エージェントはgrep/find/Readの探索ループを繰り返す代わりに、`codegraph_context`で領域マップ→`codegraph_explore`で関連ソース取得の2〜3呼び出しで完結する
- 2026-05-24: 効果の大きさはコードベース規模に比例。小規模リポジトリ（Gin・約150ファイル）では効果が縮小

## インストール

```bash
# macOS / Linux
curl -fsSL https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.sh | sh

# npm（Node.js不要バンドル版）
npx @colbymchenry/codegraph

# プロジェクト初期化
cd your-project
codegraph init -i
```

インストーラーが対象エージェントを自動検出し、`CLAUDE.md` / `.cursor/rules/` / `~/.codex/AGENTS.md` 等に設定を書き込む。
