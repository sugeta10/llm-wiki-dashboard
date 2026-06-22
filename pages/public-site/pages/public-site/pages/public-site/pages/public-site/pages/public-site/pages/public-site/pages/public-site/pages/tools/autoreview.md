# autoreview

**autoreview** は、PRをlandingする前にコードを自動レビューするAgent Skill。[[tools/openclaw]] の [agent-skills](https://github.com/openclaw/agent-skills) リポジトリで公開されている。@steipete（Peter Steinberger）が「スタックに追加した中で最もインパクトが大きいスキル」と評価。

→ https://github.com/openclaw/agent-skills/blob/main/skills/autoreview/SKILL.md

## 特徴

- PRマージ前に自動でコードレビューを実行
- エッジケースを多数検出する
- 複雑なPRでは数時間かけて実行することもある

## 関連

- [[tools/openclaw]] — autoreviewが収録されているagent-skillsリポジトリの運営元
- [[concepts/google-code-review]] — コードレビューの指針・ベストプラクティス
- [[tools/skill-cleaner]] — スキル監査CLI（autoreviewのような大きなスキルの品質チェックに利用可能）
