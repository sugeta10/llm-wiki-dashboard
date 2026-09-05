# Herdr

複数の計算機上で動くAIエージェントを1箇所に集めて管理・協作させるツール。@Michaelzsguoが、MacBook Pro・MacBook Air・Raspberry Pi 5・Dell XPS（Omarchy）・Oracleの無料VPSの5台それぞれで動くエージェントをHerdrで集中管理する運用を紹介している。

スマホからの遠隔管理はTailscale + Termiusの組み合わせで実現し、TermiusでMacBook Proにログインして `herdr` コマンドを実行すれば、手元の端末からHerdr配下の全エージェントを操作できると述べる。さらに「Herdrを直接操作する必要すらない」方法があるというが、その詳細は添付動画のみで本文からは読み取れない。Herdr自体の開発元・配布場所・対応ハーネスもこのポストからは確認できない。

## マルチペイン運用: Fable 5.1 を司令塔に限定する（@Voxyz_ai）

利用者の @Voxyz_ai は Herdr のマルチペイン構成をしばらく使い続けていると述べ、Claude と Codex にそれぞれ1ペインを与える運用を紹介している。メインエージェントが仕事を割り当て、待ち、結果を読み、フォローアップする。以前は各ウィンドウで出た結論を @Voxyz_ai 自身がウィンドウ間で運んでいたが、その役目をメインエージェントが引き取った。ChatGPT・Codex・他のエージェントを混ぜる場合は、一方が作業しもう一方がレビューする分担にしていると述べる（引用元は同アカウントの以前のポスト）。

その上で [[models/claude-fable-5-1|Fable 5.1]] がクォータを早く使い切りすぎるときは、実装には使わず Herdr 内のコーディネーターに回すと述べる。Fable が方向づけ・タスク分解・最終サインオフを担い、実装は他ペインのエージェントに任せる配分である。ポスト本文は「Frontend」で途切れリンク先に続くため、フロントエンド側の構成は未捕捉。添付画像の内容も未捕捉。

この配分は [[concepts/advisor-executor-pattern]] の Orchestrator パターン（高価モデルを司令塔に、安価モデルをワーカーに置く）を、Anthropic 外のエージェント（Codex）も含めてハーネス製品の上で組んだ形と考えられる。動機がクォータの枯渇である点は、[[concepts/fable-sprint-strategy]] で @AI_masaou が週50%制限下で Fable を Planner/Evaluator に限定した配分と同じ理由づけである。

## 問い

- Herdrの開発元・リポジトリは何か。個別ソースのingest待ち。対応エージェントは Claude と Codex にそれぞれペインを与える運用を @Voxyz_ai が示しており、少なくとも両者は扱える
- Fable をサインオフ役に限定したとき、レビューの品質はどう担保されるか。[[concepts/advisor-executor-pattern]] の fladdict 式エスカレーション（品質不足なら上位モデルで再実行）に相当する経路が Herdr にあるか
- 「直接操作しない」管理はどんな経路か（動画のみで不明）
- 1台のPC内の集約表示は[[tools/notch-multi-agent-monitor]]、PC外からの遠隔操作は[[tools/claude-code-remote-control]]が既にある。マシン横断の集中管理層が効き始めるのは何台からか

## 関連

- [[tools/claude-code-remote-control]] — 自分のPCのClaude Codeをスマホから遠隔操作する公式機能。Herdr+Tailscale+Termiusは複数マシン・ハーネス非依存で同じ目的を組む構成
- [[tools/notch-multi-agent-monitor]] — 1台のMac上で複数コーディングエージェントの状態を集約表示するアプリ。Herdrはマシン横断の集中管理
- [[tools/crabbox]] — エージェント向けリモートワークスペースのコントロールプレーン。あちらは計算をリモートへ送る方向、Herdrは各マシンのエージェントを束ねる方向
- [[models/claude-fable-5-1]] — クォータ消費が速いため実装から外し、Herdr のコーディネーターに限定する運用の対象モデル（@Voxyz_ai）
- [[concepts/advisor-executor-pattern]] — 高価モデルを司令塔・安価モデルをワーカーに置く Orchestrator パターン。Herdr のマルチペイン運用はその製品上の実例
- [[concepts/fable-sprint-strategy]] — 制限下で Fable を Planner/Evaluator に限定する配分。クォータ動機の司令塔化という同じ理由づけ
- [[concepts/claude-code-orchestration]] — 「自分が指揮官になり専門化した Claude を並べる」フレーム。Herdr ではその指揮官役をメインエージェントに渡す
