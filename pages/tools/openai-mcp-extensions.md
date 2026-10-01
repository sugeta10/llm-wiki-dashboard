# OpenAI MCP Extensions（ChatGPT プラグイン拡張）

[[companies/openai]] が公開した、MCP に ChatGPT 固有の機能を足して、外部開発者のプラグインをネイティブ機能のように振る舞わせるための拡張（Apache License 2.0）。@mxstbr は2026-09-29に「plugin extensions」として告知し、ChatGPT の meetings・health・finance などの機能を作るのに OpenAI 自身が使っているのと同じプラットフォームを外部から使えるようになった、しかも既存の MCP と MCP Apps の上に載っていると述べた。

README が示す拡張は4つで、いずれも Bits & Bolts という CAD 部品ライブラリのサンプルプラグインで実演されている。

- **Sidebar entrypoints**: サイドバーからアプリを開ける
- **File extension handlers**: 対応するファイル形式を開いたときに独自のビューア（例: CAD ファイル）を表示する
- **Composer mentions**: 入力欄からプラグインのリソースを検索し、メッセージに参照として添付する
- **Extended forms**: サムネイル選択肢などを持つフォームで入力させる

始め方は、プラグインを作り（Codex のプラグイン作成ドキュメントを参照）、MCP サーバーに SDK を入れる流れで、TypeScript は `@openai/mcp-extensions`（MCP サーバーと Apps 向け）、Python は `openai-mcp-extensions`（MCP サーバー向け）。対応拡張の詳細は同リポジトリの spec に置かれている（本vaultには未収集）。

MCP Apps は [[concepts/generative-ui]] の Open-ended パターンにあたり、本拡張はそのうえで「チャット欄の外（サイドバー・ファイル・入力欄）」にまでプラグインの入口を広げるものと読める（推論）。標準 MCP の拡張フレームワーク（[[tools/claude-mcp]] の MCP Apps 正式化）とは別に、ChatGPT 専用の拡張として提供されている点は、MCP 本体の相互運用性との兼ね合いが今後の論点になりうる（推論）。

## ChatGPT Sites での MCP サーバーホスティング

翌2026-09-30、@mxstbr は「one more launch」として、ChatGPT Sites が plugin extensions を含む MCP サーバーをホストできるようになったと告知した。告知の例では `@sites create a todo list that I can use in ChatGPT` と頼むだけで、Sites が (1) extensions つきの MCP サーバーを作り、(2) それを Sites にデプロイし、(3) MCP サーバーをプラグインに変換する、と @mxstbr は述べる。この告知は、外部開発者 @DishwasherTag の「配布は OpenAI が支援しても、バックエンドの運用は難しい。Sites のような OpenAI ホストの基盤が plugin extensions のバックエンドを担う予定はあるか」という質問への返答として出ている。

プラグイン作者がサーバーを自前で立てて運用するという参入障壁を、OpenAI 側のホスティングで肩代わりする動きと読める（推論）。Sites は [[tools/openai-codex]] の Codex Sites（成果物の Web 化）として先に登場した機能で、「作ったものをそのまま公開する場所」がプラグインの実行基盤にまで広がった形と考えられる（推論）。

## 問い

- ChatGPT Sites にホストされた MCP サーバーは ChatGPT 以外のクライアント（Claude など）からも呼べるのか、それとも ChatGPT 内専用か
- spec を読み、4つの拡張がホスト非依存の MCP 拡張として定義されているのか、ChatGPT でしか解釈されないのかを確認する
- Claude 側（MCP Apps・Claude のプラグイン）で同等の入口（サイドバー・ファイルハンドラ・メンション）はどこまで用意されているか

## 関連

- [[companies/openai]] — 提供元
- [[tools/claude-mcp]] — MCP 本体と MCP Apps の標準化
- [[concepts/generative-ui]] — MCP Apps を Open-ended パターンとして位置づける整理
- [[tools/openai-mcp-tunnel]] — 同じく OpenAI 製品に MCP サーバーをつなぐ Secure MCP Tunnel
- [[tools/openai-codex]] — プラグイン作成ドキュメントの置き場
