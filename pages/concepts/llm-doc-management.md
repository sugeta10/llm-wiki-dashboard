# LLMコンテキスト向けコードベース文書化

Claude等のLLMと協働する際、アプリの構造・フローを**HTML + JSON**の形式でドキュメント化し、そのJSONをLLMのコンテキストとして渡す設計パターン。

## DaveJ パターン（最も拡散・いいね6080）

`@DaveJ` が提唱したシングルページHTML + JSONによるフロー文書化。

> Ask Claude to document and describe the main flows in your app and output in a single page html + json data file. Incredibly useful for humans and the JSON file is very useful for explaining the flow to the LLM when working on new features/bugfixes.

### プロンプト全文

```
Create a single page html that documents workflows between packages and components in the app.
Have all the components/packages on the page and I can click on different actions
like "Invite new user" or "todesktop build" or {insert other flows here}
and then [highlight the relevant components and show the flow]
```

**出力物の二重活用:**
- **HTMLファイル**: 人間がクリックで各フローを視覚確認
- **JSONファイル**: 次のClaude依頼の冒頭に貼り付けてコンテキスト提供

## そう氏パターン（日本語版）

`@so_ainsight` による日本語プロンプト変種：

> 「このリポジトリ／このプロダクトの構成を、サービス単位で整理して。外部に見せるほどキレイじゃなくていいから、誰が読んでも迷子にならない粒度で」

HTMLで全体像を確認し、JSONを次の依頼の最初に貼る、という同じ2段活用。

## Obsidianでのドキュメント管理（GianMattya）

`@GianMattya` による別アプローチ：LLMにドキュメントを自由生成させるとコンテキストを圧迫する構造になりがち。Obsidianで管理し常に整理された構造を維持することで、開発時に必要な部分だけを読み込めるようにする。

## 関連

- [[tools/claude-code]] — このパターンの実行プラットフォーム
- [[concepts/claude-code-large-codebase]] — 大規模コードベースでの活用指針
- [[concepts/spec-driven-development]] — 仕様を文書化してLLMに渡すアプローチ
- [[concepts/implementation-notes-prompt]] — 実装と並走でノートを生成させる関連手法
- [[design/claude-design-workflow]] — HTMLでの視覚化をデザイン叩き台に使う実践（制作側からの裏付け）
- [[concepts/html-output-format]] — 同じHTML活用でも逆方向：LLMの出力フォーマットとしてHTMLを使うAnthropic公式パターン
- [[tools/html-share]] — 出力側のHTMLを一覧化・期限付き共有・スマホ承認まで運ぶセルフホストツール（minorun365製）
