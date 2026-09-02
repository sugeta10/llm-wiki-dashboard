# Google

> **TL;DR**: Google OneをストレージサブスクからAI製品の統合バンドルへ再定義。Gemini・NotebookLM・Flow・Jules・Antigravityなど多数のAI製品を3階層（AI Plus / Pro / Ultra）の利用上限で束ねて売る。

Googleの消費者向けAI戦略は、個別プロダクト課金ではなく**単一サブスクリプションで全AI製品の利用枠をまとめて配る**バンドル方式に集約されている。同じ製品（Gemini、NotebookLM、画像/動画/音楽生成、エージェント）を、ストレージ容量と「利用上限の倍率」で Plus < Pro < Ultra(5x/20x) と段階化する。AIの差別化を「どのモデルが使えるか」だけでなく「**どれだけ使えるか（rate limit）**」の軸でも価格設計している点が特徴で、ヘビーユーザーほど上位プランに引き上げる構造になっている。Geminiは [[companies/apple]] の次世代Apple Intelligenceにも技術提供されており、自社配布と他社OSへの基盤供給の両面を持つ。

## 検証済み事実

- 2026-06-09: Google Oneは無料15GB／Basic 100GB／Standard 200GBのストレージ階層に加え、AI統合プラン **Google AI Plus（400GB）/ Pro（5TB）/ Ultra（20TB〜30TB）** を提供。
- 2026-06-09: Gemini利用上限は Plus=2x、Pro=4x、Ultra=Proの5x／20x。Proプラン以上で「Proモデル」へのアクセス、Ultraで Deep Think へのアクセス。
- 2026-06-09: バンドルされるAI製品群 — **Gemini**（Live／カスタムAIエキスパート）、画像生成 **Nano Banana Pro**、音楽生成 **Lyria 3**、動画生成 **Omni Flash**、**Deep Research**、**NotebookLM**、**Google Flow**（動画生成・クレジット制）、**Jules**（エージェントタスク）、**Google Antigravity**（エージェントリクエスト）、**Project Genie**（インタラクティブ世界モデル、Ultra限定）。
- 2026-06-09: Gemini in Gmail/Docs（受信トレイへの質問・AI Overview＝米国のみ、Proofread、Vidsの動画生成）。Google検索のAI Mode（エージェント機能・Deep Search＝米国のみ）も上位プランで拡張。
- 2026-06-09: 開発者向け特典 — Google Cloudクレジット（Pro $10／Ultra 5x $40／Ultra 20x $100 月額）、Android StudioのエージェントAI支援、Jules/Antigravityの上限拡張。
- 2026-06-09: コンテキストウィンドウは有料プランで100万トークンに拡張。家族最大5人とプラン共有可能（コンテンツは各自プライベート）。
- 2026-06-09: Google Flowクレジットは Free 100/月+50/日、Pro 1,000/月、Ultra 5x 10,000/月、Ultra 20x 25,000/月。

## 観察ログ（未検証）

- 2026-06-09: 当ページは価格（"/mo"）が空欄でレンダリングされており、各プランの具体的な金額は本ソースからは取得できていない。
- 2026-06-09: AI Inbox in Gmail は "rolling out"、Gmail受信トレイへの質問・AI Overviewは「US only」表記。地域差のあるロールアウト段階の機能が混在。

## 問い

- 「利用上限の倍率」を価格軸にするバンドルは、単機能の専用ツール（[[tools/notebooklm-py]] 等の非公式クライアントや単体課金SaaS）に対してどこまで優位か。
- Geminiを [[companies/apple]] にも供給する両面戦略は、自社デバイス/OSを持たないGoogleの基盤モデル流通戦略としてどう機能するか。
- Jules・Antigravityというエージェント製品をサブスクに内包する構えは、[[tools/claude-code]] / [[tools/openai-codex]] のエージェント市場とどう競合するか。

## 関連

- [[companies/apple]] — 次世代Apple IntelligenceにGemini技術を供給（基盤モデルの他社OSへの供給先）
- [[tools/notebooklm]] — 本バンドルに含まれるNotebookLMの活用ガイド
- [[tools/notebooklm-py]] — NotebookLMの非公式Python API/CLI（Web UI非公開機能対応）
- [[tools/google-ai-studio]] — 開発者/作り手側の無料化戦略（無料アプリ構築・デプロイ・pretty URL）
- [[tools/google-knowledge-catalog]] — AIエージェント向けデータガバナンス製品（メタデータ収集・Geminiエンリッチ・検証済みクエリ）
- [[models/timesfm-3]] — Google Researchの時系列基盤モデル第3世代（2026-08-31発表）。多変量予測をゼロショット・1回の順伝播で扱い、BigQueryへ統合予定
