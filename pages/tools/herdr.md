# Herdr

複数の計算機上で動くAIエージェントを1箇所に集めて管理・協作させるツール。@Michaelzsguoが、MacBook Pro・MacBook Air・Raspberry Pi 5・Dell XPS（Omarchy）・Oracleの無料VPSの5台それぞれで動くエージェントをHerdrで集中管理する運用を紹介している。

スマホからの遠隔管理はTailscale + Termiusの組み合わせで実現し、TermiusでMacBook Proにログインして `herdr` コマンドを実行すれば、手元の端末からHerdr配下の全エージェントを操作できると述べる。さらに「Herdrを直接操作する必要すらない」方法があるというが、その詳細は添付動画のみで本文からは読み取れない。Herdr自体の開発元・配布場所・対応ハーネスもこのポストからは確認できない。

## 問い

- Herdrの開発元・リポジトリ・対応エージェント（Claude Code等）は何か。個別ソースのingest待ち
- 「直接操作しない」管理はどんな経路か（動画のみで不明）
- 1台のPC内の集約表示は[[tools/notch-multi-agent-monitor]]、PC外からの遠隔操作は[[tools/claude-code-remote-control]]が既にある。マシン横断の集中管理層が効き始めるのは何台からか

## 関連

- [[tools/claude-code-remote-control]] — 自分のPCのClaude Codeをスマホから遠隔操作する公式機能。Herdr+Tailscale+Termiusは複数マシン・ハーネス非依存で同じ目的を組む構成
- [[tools/notch-multi-agent-monitor]] — 1台のMac上で複数コーディングエージェントの状態を集約表示するアプリ。Herdrはマシン横断の集中管理
- [[tools/crabbox]] — エージェント向けリモートワークスペースのコントロールプレーン。あちらは計算をリモートへ送る方向、Herdrは各マシンのエージェントを束ねる方向
