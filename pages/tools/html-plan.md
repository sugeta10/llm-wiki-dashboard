# html-plan（Claude Code プラグイン）

Claude Code の実装プランを、1分で読めてその場で回答できる1枚の HTML ページとして出すスキル。`anthropics/claude-plugins-community` リポジトリで配布されている。作者の @trq212 は「Claude Code でより良い HTML プランを作るスキル」と紹介し、平易な言葉・コード片の提示・質問の浮上・モックアップを特徴に挙げている。@trq212 によれば、lint（生成した HTML の機械チェック）で Claude がよく陥る失敗を減らしている。告知時点では「広く出す前にフィードバックがほしい」段階だった。

## 使い方

@oikon48 の紹介によるインストール手順:

```bash
claude plugin marketplace add anthropics/claude-plugins-community
claude plugin install html-plan@claude-community
```

`/html-plan <指示>`（README の例は `/html-plan add send later to the composer`）で HTML ページを1枚生成する。単一ファイルにまとめるために `node` が必要で、ほかの依存はない。

## プランの構造: 主張の木

README によると、プランは「主張の木」として組まれ、階層ごとに答える問いと表示形式が決まっている。

| 階層 | 答える問い | 表示形式 |
| --- | --- | --- |
| タイトル | これは何か | 短い名前と、依頼者自身の言葉による「Why」 |
| 1 | 誰が何をできる・見られるようになるか | UI モックアップまたは状態機械 |
| 2 | それはどう動くか | コールスタック・スキーマ・コード片 |
| 3 | どこに書くか | コード |

閉じた状態の木がそのまま要約になり、1階層ずつ開いて読む。判断ごとに番号が振られ、下部のボタン（「3 to answer」など）でまだ開いていない次の判断へ飛べる。読み手は選択肢を選び、スキーマを編集し、任意の主張や行にコメントしてから **Respond** を押し、出てきた回答1つを Claude に貼り戻す。

成果物を「読む→要約から掘る」順に並べ、回答を1回の貼り戻しにまとめる設計は、プランのレビューで人間が散文を読み切れず質問を見落とす問題への対策と考えられる（wiki 側の推論）。完全な例は README が `skills/html-plan/examples/scheduled-send.html` を挙げている（未収集）。

## 問い

- 自分のプランモード運用（Markdown のプラン）と比べて、回答の往復回数と見落としが実際に減るか試す
- 「主張の木」の階層化は HTML でなくても（Markdown の折りたたみ等で）再現できるか。HTML 固有の利点はモックアップと Respond の集約だけか

## 関連

- [[concepts/html-output-format]] — Anthropic が示した「LLM の出力フォーマットとして HTML を使う」設計思想。html-plan はそれを実装プランの質疑に絞った実装例
- [[tools/show-me]] — コールスタック・型シグネチャなど軽い視覚表現で応答させるスキル。html-plan の階層2の表示形式と重なる
- [[tools/grill-me]] — 実装前に要件を一問ずつ掘るスキル。html-plan は質問を番号付きで一覧化し、まとめて回答させる点が対照的
- [[tools/artifact-share]] — HTML 上で指摘してエージェントに返す別経路。html-plan は貼り戻し1回で返す
- [[tools/claude-code]] — 配布先のプラグインの仕組みと HTML 出力の活用
