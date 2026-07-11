# Apple

> **TL;DR**: 自社モデル単独路線を捨て、次世代Apple IntelligenceをGoogleのGemini技術ベース「Apple Foundation Model」に置き換えるのがWWDC26の核心。Siriは「Siri AI」へ再設計される。

Appleの生成AI戦略は、2026年2月に発表したGoogleとの提携を土台に「自前モデル＋外部基盤モデル」のハイブリッドへ転換した。AI推論を**オンデバイス**（プライバシー完全保護・無料・低能力）と**Private Cloud Compute**（高能力・サーバー側・利用上限あり）の2層に分け、後者もGemini技術で刷新する。これは「プライバシーは端末で、賢さはクラウドで」という役割分担を製品全体の設計原則に据えたことを意味する。差別化の軸は素のモデル性能ではなく、写真・メモ・メッセージ等の**パーソナルコンテクスト**をOSレベルで握っている点に置かれている。

## 検証済み事実

- 2026-06-09: WWDC26基調講演を開催。今秋配信予定のOS次期バージョン群（**iOS 27** 等）を発表。OS別でなくSiri等プラットフォーム横断の共通機能としてAIアップデートを提示。
- 2026-06-09: 次世代Apple Intelligenceは、Gemini技術ベースの自社AI「**Apple Foundation Model**」をベースとする（2026年2月のGoogle提携の具体化）。
- 2026-06-09: Geminiの技術はオンデバイスAIに加え、Appleがホストするサーバー側AI「**Private Cloud Compute**」にも採用。Web検索を伴う応答生成・画像生成等に利用。
- 2026-06-09: Siriを生成AI中心に再設計し名称を「**Siri AI**」に変更。過去の写真・メモから「パーソナルコンテクスト」を理解しアクション可能に。パーソナルコンテクスト処理は主にオンデバイス実行（プライバシー保護・無料）。
- 2026-06-09: Siri AIは英語ベータを2026年後半提供予定。やり取りをチャットスレッドとして保存し、iCloud経由でiPad/Mac/Apple Watchと同期。チャット履歴はローカルと同期iCloudのみ保持。
- 2026-06-09: 標準アプリとして「Siriアプリ」を追加（履歴参照・会話継続が主目的）。
- 2026-06-09: 画像生成（Image Playground）はフォトリアル生成に対応。写真アプリにクリーンアップ強化・背景補完の拡張ツール・空間リフレームを追加。
- 2026-06-09: AIエージェント的機能 — Safariのページ更新監視通知、パスワードアプリのワンタップ自動パスワード変更（バックグラウンド実行）、ショートカットの自然言語生成、ホームアプリの通知集約。
- 2026-06-09: 開発者向けにApp Intents/Foundation Modelフレームワーク、Core AIフレームワークを強化。Core AIはApple Foundation Model以外のAIモデルもAppleシリコンでローカル実行可能に。
- 2026-06-09: Xcodeのコード生成にOpenAIのChatGPT・AnthropicのClaudeに加え**Geminiを追加**。
- 2026-06-09: システム最適化 — アプリ起動最大30%高速化、写真ライブラリ読み込み最大70%高速化、AirDrop最大80%高速化。iOS 27はiOS 26同様iPhone 11以降に対応。

## 観察ログ（未検証）

- 2026-06-09: 記者観測 — Siri AIのチャットログはモデル学習に使われないと見られる（他社生成AIと差別化、iCloud暗号化が適用される可能性が高い）。
- 2026-06-09: 記者観測 — パスワード自動変更はオンデバイスAI/Private Cloud Computeが各サイトにアクセスし手続きを自動実行するエージェント機能と推測。
- 2026-06-09: 記者観測 — 最もパワフルなオンデバイスモデルや高度な音声機能は「最も高性能なiPhone/iPad/Mac」（iPhone 17 Air/17 Pro以降のProチップ世代）限定。日本語での音声認識精度向上の挙動は不明。
- 2026-06-09: 記者観測 — Mac mini/Mac Studioが大規模LLMローカル実行需要で伸びている背景があり、Core AIのローカル実行強化はその動きへの対応の一環と見られる。

## 問い

- AppleがGeminiに基盤を委ねたことで、Apple独自モデルの位置づけ（差別化の源泉）はオンデバイス＋パーソナルコンテクストだけに収斂するのか。[[companies/google]] への依存リスクは。
- 「プライバシーは端末・賢さはクラウド」の2層分担は、ユーザー体験として一貫性を保てるか（どの処理がどちらに行くかの不透明さ）。
- Xcodeへのモデル選択肢拡大（ChatGPT/Claude/Gemini）は、[[tools/claude-code]] 等の外部エージェント利用とどう棲み分かれるか。

## 関連

- [[companies/google]] — 次世代Apple IntelligenceとPrivate Cloud Computeに採用されるGemini技術の提供元
- [[companies/openai]] — Xcodeのコード生成で選択可能なChatGPTの提供元
- [[companies/anthropic]] — Xcodeのコード生成で選択可能なClaudeの提供元
- [[tools/emil-kowalski-skills]] — WWDCデザイントークから17のデザイン・モーション原則を蒸留した/apple-designスキル（Emil Kowalski製）
