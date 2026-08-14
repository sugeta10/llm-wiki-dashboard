# HTML共有くん（html-share）

minorun365が公開するセルフホスト型のHTML共有ツール。Claude Codeの生成物を見やすいHTMLに整え、自分専用の一覧にまとめ、スマホから確認・共有できるようにする。初回セットアップ後は日本語でClaude Codeに頼むだけで動く、というのが作者の説明である。

セルフホスト型で、自分のAWSアカウント上で動かす。作者側にページや回答が送られない設計だと明記されている。

## できること

- メモや調査結果を、見やすいHTMLに整える
- 作ったHTMLを自分専用の一覧へまとめる
- 社内限定や期限付きのURLで共有する
- AIからの承認依頼をスマホで確認し、コメントを返す
- スマホから返した内容を、PC上の作業へ引き継ぐ

## 頼み方

コマンドを覚える必要はなく、Claude Codeへ日本語で頼むと登録・共有URL発行・スマホへの確認依頼まで進む、と作者は述べる。README掲載の例は次のとおり。

> このHTMLを共有くんに追加して
> この内容を見やすいHTMLにまとめて
> このページを社内限定で7日間共有して
> `/mobile` で、今の作業をスマホから確認できるようにして

導入手順は同リポジトリの `docs/setup.md`、公開前に確認する仕組みは `docs/threat-model.md`（セキュリティ設計）にまとめられている。

このツールが埋めているのは「LLMの出力をHTMLで受け取る」（[[concepts/html-output-format]]）と「PCの前を離れても作業を握り続ける」（[[tools/claude-code-remote-control]]）の交差点にある隙間だと考えられる。承認待ちで人間がPCの前にいなければならない時間を、共有URLとスマホ側のコメント返しに置き換える発想になっている。

## 問い

- スマホからのコメントをPC上の作業へ戻す経路は、Claude Codeのどの機構（hook・MCP・remote control）で実装されているか。`/mobile` はこのツールが追加するスラッシュコマンドか
- 期限付き・社内限定URLの権限モデルは `docs/threat-model.md` でどこまで詰められているか。セルフホストなら共有範囲の責任は運用側に来る
- このvaultの `wiki/outputs/` に出すHTML（CLAUDE.mdのUC-2）を外に見せたいとき、同じ仕組みを使えるか

## 関連

- [[concepts/html-output-format]] — LLMの出力フォーマットとしてHTMLを使うAnthropicの設計思想。HTML共有くんはその出力の置き場所と配布経路を用意する
- [[tools/claude-code-remote-control]] — スマホ・ブラウザからPC上のClaude Codeを操作する公式機能。こちらは操作そのものを遠隔化し、HTML共有くんは成果物の確認と承認を遠隔化する
- [[concepts/llm-doc-management]] — HTML+JSONでアプリのフローを文書化しLLMのコンテキストに渡すパターン。同じHTMLでもこちらは入力側、HTML共有くんは出力側
