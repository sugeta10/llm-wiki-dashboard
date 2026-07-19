# Dynamic Agent Org（グラフによるエージェント組織のプログラム化）

@Saboo_Shubham_ は、@steipete の「まだloopの話をしているのか、それともgraphの話に移ったのか」という問いかけへの返信で、「[[concepts/loop-engineering|loop]] がエージェント単体の振る舞いをプログラム可能にしたのに対し、graph はエージェントの組織構造そのものをプログラム可能にする」と整理した。その上で **Dynamic Agent Org** を「作業が進行している最中にグラフ自体が書き換わる」状態と定義する。

[[concepts/multi-agent-patterns]] のPipeline・Fan-Out・Message Bus・Shared Stateといった既存パターンは、いずれも実行前に接続構成（誰が誰に何を渡すか）を固定してから走らせる静的グラフだと考えられる。Dynamic Agent Orgが指すのは、実行中に発見された事実に応じてエージェントの追加・削除・再配線が起きる、より高い抽象化レイヤーだと読める。[[concepts/loop-engineering]] のStage 5「orchestration 2026: loops supervise loops」がループ同士の階層化を指すのに対し、こちらは組織図（グラフ構造）そのものの可変性に焦点を当てている点で軸が異なる。

短文ポストのため、具体的な実装方法（グラフ書き換えのトリガーや検証機構）は明かされていない。

## 問い

- 「グラフが実行中に自分自身を書き換える」を具体的に実装するとどうなるか。エージェントが新しいエージェントを動的に生成・接続する仕組みは [[concepts/claude-code-dynamic-workflows]] に近いのではないか。
- Dynamic Agent Orgに終了条件・予算上限を設計しないと、[[concepts/loop-engineering]] の失敗パターン（暴走・理解の負債）がグラフ全体に拡大するリスクがあるのではないか。

## 関連

- [[concepts/loop-engineering]]
- [[concepts/goal-loop-routine]]
- [[concepts/multi-agent-patterns]]
- [[concepts/claude-code-dynamic-workflows]]
