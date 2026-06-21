# obsidian-skills

**obsidian-skills** は [kepano](https://github.com/kepano)（Obsidian CEO、Steph Ango）が作成した Obsidian 向け **Agent Skills** 公式コレクション。[Agent Skills 仕様](https://agentskills.io/specification) に準拠しており、[[tools/claude-code]]・Codex CLI・OpenCode など複数のエージェントで共通して利用できる。

## スキル一覧

| スキル | 概要 |
|-------|------|
| `obsidian-markdown` | Obsidian Flavored Markdown（wikilinks, embeds, callouts, properties）の作成・編集 |
| `obsidian-bases` | Obsidian Bases（`.base`）のビュー・フィルタ・数式・集計の作成・編集 |
| `json-canvas` | JSON Canvas（`.canvas`）のノード・エッジ・グループの作成・編集 |
| `obsidian-cli` | Obsidian CLI 経由でのvault操作、プラグイン・テーマ開発 |
| `defuddle` | Defuddle CLI でWebページからクリーンなMarkdownを抽出してトークンを節約 |

## インストール方法

### Claude Code（Marketplace）

```
/plugin marketplace add kepano/obsidian-skills
/plugin install obsidian@obsidian-skills
```

### npx skills

```bash
npx skills add git@github.com:kepano/obsidian-skills.git
# または HTTPS
npx skills add https://github.com/kepano/obsidian-skills
```

### 手動（Claude Code）

リポジトリの内容を Obsidian vault 直下の `/.claude` フォルダに配置する。

### OpenCode

```bash
git clone https://github.com/kepano/obsidian-skills.git ~/.opencode/skills/obsidian-skills
```

`~/.opencode/skills/` 以下の `SKILL.md` を自動検出。設定変更不要で再起動後に有効。

## Agent Skills 仕様

[agentskills.io/specification](https://agentskills.io/specification) で規定された共通スキル仕様。Claude Code・Codex CLI など複数エージェントが同じスキルファイルを使い回せる設計。スキルは `SKILL.md` として定義し、エージェントが必要時にロードする。

## 関連

- [[tools/claude-code]]（Claude Code での利用、Marketplace経由のインストール）
- [[tools/openai-codex]]（Codex CLI での利用）
- [[tools/google-agent-skills]]（Google製Agent Skills集・同じagentskills.io仕様）
- [[concepts/llm-wiki]]（ObsidianでのLLMナレッジベース管理）
- [[concepts/obsidian-personal-os]]（ObsidianをAI自動化でパーソナルOSとして設計するアーキテクチャ）
- [[tools/taskmd-shelf]]（1タスク1Markdownで管理するタスク手法・Obsidian + Dataview連携）
- [[concepts/obsidian-claude-second-brain]]（Obsidian+Claude第二の脳listicle・本スキルを「3ヶ月で12,900+ stars」と言及）
