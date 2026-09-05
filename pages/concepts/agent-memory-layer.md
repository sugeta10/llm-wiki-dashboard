# 共有エージェントメモリ層（One Memory Layer）

> **TL;DR**: 各AIエージェントに独立した記憶を持たせるのではなく、ユーザーが所有する単一のメモリ層を全エージェントの下に敷く設計思想。あるエージェントが学んだことを即座に他のエージェントも使えるようにする。

複数のAIエージェントを使い分けると、それぞれが「自分専用の脳」を持ち、ユーザーや作業についての断片的な像しか持たなくなる。新しい人に会うたびに自己紹介をやり直す人間の「知識は頭蓋骨の中にあり、頭蓋骨は同期しない」という制約を、わざわざソフトウェアの中に再現してしまっている状態である。エージェントは本来この制約を持つ必要がないため、共有メモリ層を敷けば、ある場で得た知見が部屋を越えて即座に他のエージェントへ波及する「ハイブマインド」的な振る舞いが可能になる。

核心は「リポジトリはメモリではない」という指摘にある。Markdownや計画書、決定ログをリポジトリに残すのは**到達点（destination）の保存**であり、そこに至る**過程（journey）= セッションそのもの**——試行錯誤、捨てた分岐、後で蘇る半端なアイデア——は失われる。計画を文章に固めた時点で会話は圧縮され、結論だけ残して経路の大半が捨てられる。数日後にその経路が再び必要になったとき、リポジトリ同期では解決できない。残すべき単位は「保持する価値のあるもの（the thing worth keeping）」だけであり、全トランスクリプトのばら撒きではない（ノイズ・機密・誤り・期限切れを含むため）。

断片化は概念的なだけでなく物理的でもある。エージェントは異なるマシン・ファイルシステム・ローカル状態・クラウドサービスに分散し、リポジトリはGitHub経由で同期できてもプロジェクトの記憶は同期しない。これは今後1年の重要な開発領域とされ、既存の取り組みが問題を異なる角度から攻めている。

## 観察ログ（未検証）

- 2026-05-31: @pejmanjohn のエッセイ "Stop Giving Every Agent Its Own Skull"。自身は OpenClaw（個人アシスタント・アイデア発展）/ Codex（実装）/ Claude Code（デザイン・執筆）を役割分担で使い分けるが、アイデアを生んだ推論が OpenClaw に取り残され、他エージェントは「有能だが文脈盲目（competent and context-blind）」な出力を返すと指摘。
- 2026-05-31: 関連プロジェクトとして @garrytan の **GBrain**（MCPの背後に共有ナレッジグラフを置き、各エージェントが自前メモリを持たず共有グラフをクエリする）と、@doodlestein の **CASS**（Codex/Claude Code/OpenClaw/Cursor/Aider 等のローカルセッション履歴を横断検索可能にし、リポジトリが取りこぼす推論を拾う）を「答えの重要なピースが見え始めた兆候」として挙げる。
- 2026-05-31: 企業版の例えとして「10の会議に同時出席するAI版リーダー」を提示。価格への顧客の混乱・製品チームの価格議論・営業の失注理由が、人間なら数日〜数週かかる点の接続を会議進行中に衝突させられる、と主張。
- 2026-06-04: @shannholmberg が自社エージェンシー内に構築したgBrainの階層構造を公開。Agency gBrain → Orchestrator [[tools/hermes-agent]] → Department verticals → Specialist agents → Scoped sub-agentsという5段構成で、[[tools/hermes-agent]]をオーケストレーター（各部門・専門エージェントを統括する上位層）として据える具体例。
- 2026-06-05: Y Combinator President の @garrytan がこの投稿を引用し「GBrainはあなたの会社の脳」と賛同。GBrainが個人の一開発者コミュニティに留まらない注目を集めている兆候

## 問い

- 「保持する価値のあるもの」を会話から自動抽出する基準をどう設計するか。[[concepts/managed-agents-dreams]] の重複削除・矛盾解消パイプラインがこの選別を担えるか。
- 自分の [[concepts/llm-wiki]]（Markdownベース知識ベース）は「到達点の保存」に偏っていないか。捨てた分岐・推論経路を残す仕組みが必要か。
- 共有メモリ層は [[papers/2026-peng-llm-memory-faulty]] が示すメモリ劣化を増幅しないか（全エージェントが汚染された記憶を共有するリスク）。

## 関連

関連: [[concepts/managed-agents-dreams]] · [[concepts/llm-wiki]] · [[papers/2026-peng-llm-memory-faulty]] · [[tools/openclaw]] · [[people/garry-tan]] · [[concepts/ai-session-handover]] · [[concepts/openai-data-agent-context-layers]] · [[concepts/philosophy-external-memory]]（個人版：思想を単一の外部記憶に出し全エージェントに参照させる） · [[concepts/skills-over-memory]]（push/pull の切り分け：recall は削除でなく pull 側へ移す） · [[concepts/cerebras-knowledge-base]]（企業版：全ツール・全ソースを単一埋め込みテーブルに集約する設計） · [[concepts/company-brain]]（企業版：単一state層をprovenance/permissions/freshness付きで持ち役割ごとのontologyで読み出す設計・@ashwingop） · [[concepts/llm-wiki-vs-company-brain]]（GBrainが登場する個人〜企業境界線の議論をKarpathy式LLM Wikiとの5つの分岐点で整理） · [[concepts/knowledge-graph-llm]]（「長期記憶がない」を記憶の所有単位でなく知識の構造＝ナレッジグラフ側から解く対案） · [[concepts/self-owned-ai-memory]]（個人DIY版：要約→ベクトル化→タグ→関連付け→忘却曲線→常時稼働の無意識層で、モデル・サービスに依存しない記憶を自分で持つ・@kuwagata_no_isi／@zapabob_ouj）
