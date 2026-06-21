# notebooklm-py

[[tools/notebooklm]] の非公式Pythonライブラリ。WebインターフェースのAPIに未公開機能を含む完全なプログラムアクセスを提供し、Claude Code・Codex・OpenClaw等のAIエージェントと統合可能。

> ⚠️ **非公式ライブラリ**: Googleの非公開内部APIを使用しており、予告なく変更される可能性がある。プロトタイプ・研究・個人プロジェクト向け推奨。

## インストール

```bash
pip install notebooklm-py
# ブラウザログイン対応版
pip install "notebooklm-py[browser]"
playwright install chromium
```

## 主な機能

### 研究自動化
- URLs・PDFs・YouTube・Google Drive からソースを一括インポート
- Web/Drive リサーチクエリ（fast/deep モード）+ 自動インポート
- 再現可能なリサーチパイプライン構築

### コンテンツ生成

| 生成タイプ | オプション | ダウンロード形式 |
|-----------|-----------|----------------|
| Audio Overview（ポッドキャスト） | 4形式・3長さ・50+言語 | MP3/MP4 |
| Video Overview | 3形式・9スタイル + Cinematic | MP4 |
| Slide Deck | 詳細/プレゼン形式・スライド個別改訂 | PDF/PPTX |
| Infographic | 3方向・3詳細度 | PNG |
| Quiz・Flashcards | 量・難易度設定 | JSON/Markdown/HTML |
| Report | briefing/study guide/blog/カスタム | Markdown |
| Mind Map | 階層可視化 | JSON |
| Data Table | 自然言語でカスタム構造 | CSV |

### Web UIでは非公開の機能
- **バッチダウンロード**: 同タイプの全アーティファクトを一括取得
- **Quiz/Flashcard エクスポート**: JSON・Markdown・HTML（Web UIはインタラクティブ表示のみ）
- **Mind Map JSONエクスポート**: 可視化ツール向け階層JSON
- **Data Table CSV**: スプレッドシートとして保存
- **Slide Deck as PPTX**: PowerPoint形式（Web UIはPDFのみ）
- **スライド個別改訂**: 自然言語プロンプトで特定スライドを修正
- **プログラム共有**: UIを介さず権限管理

## AIエージェント統合

```bash
# Claude Codeスキルとしてインストール
notebooklm skill install
# ~/.claude/skills/notebooklm に展開される

# npx経由でインストール（Superpowers skills ecosystem対応）
npx skills add teng-lin/notebooklm-py
```

Claude Code・Codex・OpenClaw（[[tools/openclaw]]）等のLLMエージェントから自然言語でNotebookLMを操作できる。

## Python API（非同期）

```python
from notebooklm import NotebookLMClient
async with await NotebookLMClient.from_storage() as client:
    nb = await client.notebooks.create("Research")
    await client.sources.add_url(nb.id, "https://example.com", wait=True)
    result = await client.chat.ask(nb.id, "Summarize this")
    await client.artifacts.generate_audio(nb.id)
```

## 関連

- [[tools/claude-code]]
- [[tools/openclaw]]
- [[concepts/llm-wiki]]
- [[concepts/modular-research-pipeline]] — 本ライブラリを「分析エンジン」層として組み込む研究ワークフロー
- [[companies/google]] — NotebookLM本体を含むGoogle OneのAIサブスクバンドル
- [[tools/papers-cli]] — arXiv/J-STAGE/IRDBから論文PDFを集めるCLI。取得したPDFを本ライブラリでNotebookLMに流すパイプラインが組める
