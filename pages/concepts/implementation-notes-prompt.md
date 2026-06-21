# 実装ノート並走プロンプト（Implementation Notes Prompt）

LLMに仕様実装を依頼する際、**実装ノートファイルを同時に書き続けさせる**プロンプト手法。Anthropicエンジニアが実践し広まった。

## プロンプト（英語原文）

```
Implement <SPEC>. As you work, maintain a running implementation-notes.html file
that captures anything I should know about how the implementation deviates from
or interprets the spec, including:
- Decisions you had to make that weren't in the spec
- Things you had to change
- Tradeoffs you made
- Anything else I should know
```

## なぜ有効か

仕様書があっても**曖昧さ・未知の未知（unknown unknowns）**は必ず残る。LLMが実装中に判断した内容を記録させることで：

- 人間がレビュー時に「なぜこうなったか」を追跡できる
- AIが独断で進む部分を可視化し、人間をループに残す
- 振り返り・次スプリントの仕様改善に使える

> 「specがあっても必ず曖昧さと未知の未知が出てくる。これでモデルに判断の逃げ道を与えつつ、人間をループに残せる」— @trq212（Anthropicエンジニア）

## 形式

- `implementation-notes.html` が推奨（構造化しやすい）
- Markdownでも可
- 実装と**並走**させる点が重要（事後ではなくリアルタイム更新）

## [[concepts/spec-driven-development]] との関係

**Spec-anchored**（実装と並走して書き直す生きたドキュメント）の具体的な実践形。仕様書とは逆方向のドキュメント——「AIが何をどう判断したか」を捕捉する。

## 関連

- [[concepts/spec-driven-development]] — 仕様駆動開発・Spec-anchoredアプローチ
- [[concepts/prompt-engineering]] — プロンプト設計全般
- [[concepts/agentic-coding]] — AIエージェントによるコード生成
- [[concepts/ai-teacher-prompt]] — 作業後に教師役で理解を補完する姉妹手法（同じ@trq212発・人間をループに残す）
