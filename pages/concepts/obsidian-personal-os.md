# Obsidian パーソナルOS設計

ObsidianをAI自動化による「崩壊しないパーソナルオペレーティングシステム」として設計するアーキテクチャパターン（@cyrilXBT 提唱）。**Storage（Obsidian）→ Intelligence（Claude Code + MCP）→ Automation（N8N）**の3層構造で、生産性システムが壊れる3要因（手動メンテ負担・複雑化・知性ゼロ）を解決する。

関連: [[tools/claude-code-obsidian-project]] / [[tools/obsidian-skills]] / [[concepts/llm-wiki]] / [[concepts/claude-code-work-folder]]（Claude Code単体・初心者向けに最小化した同思想） / [[concepts/modular-research-pipeline]]（Obsidianを記憶層に使う研究ワークフローの具体例） / [[concepts/obsidian-claude-second-brain]]（プラグイン/ワークフロー網羅型のlisticle視点） / [[concepts/obsidian-second-brain-setup]]（install→autopilotの初心者向け構築手順視点）

## 観察ログ（未検証）

- 2026-05-21: **3層アーキテクチャ** — Layer 1: Storage（Obsidian本体・プレーンテキスト、人間/機械可読・永続）/ Layer 2: Intelligence（Claude Code + Filesystem MCP でvaultを読み・横断接続し・更新）/ Layer 3: Automation（N8N on $5サーバー、スケジューリング・API呼び出し・システム間データ転送）。「ツールを使う」から「システムが動く」への転換は3層すべてが揃うことで起きる
- 2026-05-21: **8フォルダ固定構造** — `00-CAPTURE`（未処理の一次受け・判断不要）/ `01-ACTIVE`（projects/areas/daily）/ `02-RESOURCES`（参照ライブラリ）/ `03-SYSTEM`（CLAUDE.md・skills/・workflows/・logs/）/ `04-GENERATED`（Claude出力専用・手動編集禁止）/ `05-QUEUE`（Claudeへのタスク投入口）/ `06-CALENDAR`（時系列情報）/ `07-ARCHIVE`（完了・陳腐化、削除せずここへ）。フォルダ数は固定・相互排他・重複なし
- 2026-05-21: **CLAUDE.md単一ソース原則** — Identity・Life Areas（Health/Finance/Relationships/Learning/Career）・Active Projects・Current Priorities・Standards・Operating Rules を1ファイルに集約。優先度変更時はCLAUDE.md 1ファイルだけ更新すれば以降の全ワークフローに伝播。毎週月曜にCurrent Prioritiesのみ5分更新が唯一の必須メンテ習慣
- 2026-05-21: **5つの自動ワークフロー**（N8N cron）
  - ① Morning Briefing（毎朝6時）: CLAUDE.md読んで「今日の最優先1件・当日イベント・オープンループ・プロジェクトパルス」を300語以内で生成 → `GENERATED/briefings/[DATE]-morning.md`
  - ② Capture Processor（毎晩20時）: 当日の00-CAPTUREファイルをTASK/IDEA/REFERENCE/NOTE/EVENTに分類して自動配置・アーカイブ
  - ③ Weekly Review Generator（毎週日曜19時）: 過去7日のデイリーノート+プロジェクト変更を分析しレビュー生成・CLAUDE.md Current Priorities自動更新
  - ④ Queue Processor（2時間毎）: `05-QUEUE` のファイル名（動詞+トピック形式: `RESEARCH-xxx.md`、`DRAFT-yyy.md` 等）を解析してタスク実行・`GENERATED` に出力
  - ⑤ Project Health Monitor（毎週月曜7時）: 全アクティブプロジェクトのSTATUS/EVIDENCE/NEXT ACTIONを評価。7日無活動はQUEUEに `REVIEW-[project].md` を自動生成
- 2026-05-21: **崩壊防止メカニズム3点** — ① 00-CAPTUREキャプチャ安全網（忙しい日も「とりあえず全部CAPTURE」でOK、processor夜間処理）② ARCHIVE削除禁止ルール（完了物は削除せず07-ARCHIVEへ移動、日常操作では参照しない）③ CLAUDE.md単一真実（5分更新で全ワークフローに反映）
- 2026-05-21: **HTMLをiframeでObsidian上に表示** — Markdown形式の制約を超えて、HTMLをiframeとして埋め込むことでリッチな視覚表現をvault内で確認できる手法（@shotovim）。「HTMLは書きづらいがiframeで埋め込むと閲覧体験が良い」という評価。CLAUDE.md等のルールで `wiki/outputs/YYYY-MM-DD-{topic}.html` に保存したファイルをObsidianで確認する際にも応用可能
- 2026-05-21: **1ウィークエンド構築ロードマップ** — 土曜AM(2h): 8フォルダ作成+CLAUDE.md自分情報で記入+最初のプロジェクトoverview作成 → 土曜PM(1h): Claude Desktop + Filesystem MCP設定+Morning Briefing手動実行で動作確認 → 土曜夜(30m): QUEUEに実タスク投入してアウトプット体感 → 日曜AM(2h): N8N構築+Morning Briefing cron化 → 日曜PM(2h): 残り4ワークフロー追加
