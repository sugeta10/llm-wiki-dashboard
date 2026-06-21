# NotebookLM

Googleが提供するAI搭載のノートブックツール。PDFやWebページをソースとして取り込み、Q&A・要約・ポッドキャスト生成などを行える。

## カスタムプロンプト

`@ai_jitan`（2026-05-16）による記事「NotebookLMカスタムプロンプトの全てを、本気で全部書く。」— NotebookLMのカスタムプロンプト機能を徹底解説。標準的なQ&Aを超えた高度な活用が可能になる。

## 隠し機能・裏技10選

`@ai_jitan`（2026-05-14）による記事「NotebookLMの隠し機能と裏技10選を完全解説」— Web UIから見えにくい機能を10個厳選紹介。

## Obsidian × Claude × NotebookLM 読書自動化

@leopardracer による実践（2026-05-12）。「1ヶ月で本を1冊も開かずに34冊分の知識を吸収した」と報告：

1. Obsidianにvaultを構築し、書籍PDF等を投入
2. Claudeと連携してコンテンツを処理
3. NotebookLMを追加接続

**自動生成されるもの**: フラッシュカード・マインドマップ・自分のノートからのみ回答するAI

[[concepts/obsidian-personal-os]] の実践事例の一つ。

## カスタムプロンプトの応用：本と対話する読書術

`@ai_jitan`（2026-06-05）が「本と対話する読書術」をプロンプトごと全公開。カスタムプロンプト次第で NotebookLM を Q&A ツールの枠を超えて使える、という実例:

- **本を擬人化して対話させる** — ソースの書籍を「話し相手」として振る舞わせ、内容を引き出す
- **推理小説をゲーム化** — ストーリーをインタラクティブに「遊べる」体験に変換

ポイントは、NotebookLM の価値はソース投入の量ではなく**カスタムプロンプトの設計次第で何でもできる**こと。上の「カスタムプロンプト」節の徹底解説に続く、具体的な応用パターン。

## Web Clipper for NotebookLM 拡張（Docs/Sheets/Slides対応）

ブラウザ拡張「Web Clipper for NotebookLM」が v1.19.0 で **Google Docs・スプレッドシート・スライドのツールバーから1クリックでソース追加**できるようになった（@Majin_AppSheet 経由・2026-06-05）。従来はNotebookLM側からDriveピッカーを掘ってソースを探す必要があったが、逆に**ソース側からどのノートブックに追加するか選べる**動線になった。

ポイントは、MarkdownやテキストへのHTML変換でなく**Googleソースとしてそのまま入る**こと。そのため AutoSync が効き、元ドキュメントの更新が**live source として追従**する（静的コピーにならない）。

## Google Antigravity 経由での操作自動化

@tetumemo（2026-05-20）が、Google Antigravity から NotebookLM操作の Skill を呼び出し「本日分をお願い」と依頼するだけで、Manus で調査済みの日刊AI系ニュースまとめファイルを取得して NotebookLM に反映する自動化を報告。Antigravity は Gemini 3.5 ベースのため、Codex / [[tools/claude-code]] より応答が速いと評価している。

ポイントは、NotebookLM を **AIコーディングIDE側のスキルから操作する**ことで、日次の調査→ソース投入のルーチンを「一言の指示」に畳める点。[[tools/notebooklm-py]] の Python CLI が API 直叩きの自動化なのに対し、こちらは IDE のエージェント＋Skill 経由で同じことをノーコードで回す動線。

**補足（@m13v_, 2026-05-20）**: 出力がテキスト Q&A で止まる点を指摘。通勤・散歩で聞き流せる **audio まで自動化**（podlog.io 等）すると、リポジトリ／情報の追跡が習慣として定着しやすい、と提案している。

## 関連

- [[tools/notebooklm-py]] — NotebookLM非公式Python API/CLI（Web UI非公開機能をバッチ操作）
- [[tools/notebooklm-business-workflows]] — 業務時短9ワークフロー（会議・資料・申請書・改善ループ）
- [[concepts/obsidian-personal-os]] — Obsidian+Claude Code+N8NによるパーソナルOS設計
- [[companies/google]] — NotebookLMを含むGoogle OneのAIサブスクバンドル
