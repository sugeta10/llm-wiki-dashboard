Matt Pocock（Total TypeScript作者・~60,000人ニュースレター読者）が公開するClaude Code・Codex等対応のエンジニアリングスキルコレクション。「**Vibe Codingではなく実際のエンジニアリング**」を標榜し、小規模・適応可能・コンポーザブルな設計が特徴。GSD/BMAD/Spec-Kitのようなプロセス全体を掌握するフレームワークと異なり、ユーザーのコントロールを奪わない。

`npx skills@latest add mattpocock/skills` で初期インストール、`/setup-matt-pocock-skills` で各リポジトリ設定（イシュートラッカー・ラベル・ドキュメント先）を構成する。

## スキル一覧

### Engineering（日常的コード作業）

| スキル | 目的 |
|--------|------|
| `/grill-with-docs` | 要件深掘り対話 + CONTEXT.md（共有語彙）+ ADR（アーキテクチャ決定記録）の更新 |
| `/tdd` | Red-Green-Refactorループ。失敗テスト先行でフィードバックループを担保 |
| `/diagnose` | 難バグ・性能劣化の規律ある診断ループ（再現→最小化→仮説→計装→修正→回帰テスト） |
| `/triage` | ステートマシン形式のイシュートリアージ |
| `/to-prd` | 会話コンテキストからPRDを生成しGitHubイシューに投稿 |
| `/to-issues` | 計画・仕様・PRDを垂直スライス形式のGitHubイシューに分解 |
| `/zoom-out` | コードの広いコンテキストや高レベル視点を要求 |
| `/improve-codebase-architecture` | Ball of Mud救済。CONTEXT.mdとADRを参照しDeepening機会を特定 |
| `/prototype` | 状態/ビジネスロジック問題にはターミナルアプリ、UI設計には複数バリエーション |

### Productivity（コード以外の汎用ワークフロー）

| スキル | 目的 |
|--------|------|
| `/grill-me` | あらゆる計画・設計の徹底的要件掘り下げ対話（→[[tools/grill-me]]） |
| `/caveman` | Ultra圧縮コミュニケーションモード。フィラーを削除してトークン約75%削減 |
| `/handoff` | 会話を引き継ぎドキュメントに圧縮して別エージェントへバトン |
| `/write-a-skill` | 適切な構造・プログレッシブ開示・バンドルリソースを持つスキルを新規作成 |

## 設計哲学

Pragmatic ProgrammerやDomain-Driven Designの古典的知見をAIエージェント時代に移植：
- **整合（Alignment）**: `/grill-with-docs`でエージェントとユーザーの共通語彙を構築
- **フィードバックループ**: `/tdd`でテストが赤→緑の変化でエージェントに一貫したシグナルを提供
- **設計への投資**: エージェントは開発速度を上げると同時にソフトウェアエントロピーも加速させる。設計スキルで相殺

## 観察ログ（未検証）

- 2026-06-22 @opensourcelab9: 「AIにちゃんとした開発をさせたいなら、まず mattpocock/skills を見てください」と別アカウントが再紹介。推しポイントは本ページ冒頭と同じ「GSDやSpec-Kitみたいに手順を奪わない／自分でコントロールしながらAIに任せられる」点で、コントロール非剥奪という設計思想が複数の発信者に独立して評価されていることを示す（一次の著者発信でなく二次紹介・X bookmark 99、2026-06-22時点）

## 関連

- [[tools/grill-me]] — このコレクションの最人気スキル（詳細ページ）
- [[tools/claude-code]] — 主要実行プラットフォーム
- [[tools/compound-engineering]] — 同様の計画先行思想を持つプラグイン
- [[tools/shaping-skills]] — Shape Up由来の補完的計画スキル群
- [[concepts/spec-driven-development]] — 仕様先行開発との接続
