# SaaS inside AI Agents（AIエージェントがワークスペースになる時代）

> **TL;DR**: 「SaaSにAIを組み込む」のではなく、AIエージェント（Codex・Claude Code）を主作業環境とし、その中のin-appブラウザでSaaSツールを使う——という主従逆転のワークフロー仮説。

これまでの主流は、既存のSaaSプロダクトにAI機能を後付けで埋め込む方向（SaaS + AI）だった——入口はあくまでSaaSアプリで、AIはその一機能にとどまる。**SaaS inside AI Agents** はこの向きを反転させ、作業の起点をAIエージェント側に置く。SaaSは独立した入口ではなく、エージェント内のブラウザから必要に応じて呼び出されるサブツールへと格下げされる。

構造としては、エージェントが「人間が直接アプリを操作する」という前提を吸収し、ドキュメント作成・調査・ツール横断の操作をエージェント側で完結させる。SaaSは独立した入口ではなく、エージェントが必要に応じて開く部品になる。これは、コマンドラインツールやスタンドアロンSaaSを人間が直接叩く従来モデルに対する代替であり、[[tools/openai-codex]] や [[tools/claude-code]] のようなコーディングエージェントが汎用作業環境へと用途拡張していく流れと整合する。

読者にとっての含意は、ツール選定・プロダクト設計の軸が「単体で使われるSaaS」から「エージェントから呼び出される/エージェントに統合される前提のツール」へ移りうる、という点にある。[[business/ai-agent-era-strategy]]（権限設計・ToA等）や [[business/ai-vertical-integration]]（垂直統合とSaaS業界への影響）とあわせて、エージェント中心時代のプロダクト戦略を考える材料になる。

## 観察ログ（未検証）

- 2026-05-25: Dan Shipper（Every.to CEO、Lenny's Podcast出演）の予測「未来の仕事はCodexまたはClaude Codeの中で行われる。SaaSツールにAIを入れるのではなく、AIエージェントのin-appブラウザの中でSaaSツールを使う」
- 2026-05-25: Dan Shipper本人はすでに全作業をCodexで行っている（ドキュメント作成を含む）と述べた——単一ソースの自己申告
- 2026-05-25: 引用ツイート「Automation is a lie. CLIs are over. The SaaSpocalypse is dumb.」——1年前の予測が概ね的中したとの主張（Claude Codeの台頭含む）
- 2026-05-25: @lennysan評: Danは1年前のポッドキャスト収録時点ですでにClaude Codeを注目株として挙げていた

## 問い

- 「エージェント内ブラウザでSaaSを使う」は本当にプロダクションの日常業務で成立するか、それとも開発者中心の早期事例にとどまるか？
- SaaS側はエージェント統合（API・権限委譲）でこの流れに乗るのか、入口を奪われて防御に回るのか——[[business/ai-vertical-integration]] と突き合わせて検討する。

## 関連

- [[business/ai-agent-era-strategy]] — AIエージェント時代の企業戦略5テーマ（ToA・Authorize権限設計等）
- [[business/ai-vertical-integration]] — AI企業の垂直統合とSaaS受託業界への影響（fladdict分析）
- [[tools/openai-codex]] — Dan Shipperが主作業環境として使うCodex
- [[tools/claude-code]] — 競合ポジションにありながらDanが事前に注目していたツール
- [[business/startups-for-agents]] — 「エージェントがデフォルトで手を伸ばすツールを作れ」という供給側の投資テーゼ（@gregisenberg）。需要側の本ページと表裏
