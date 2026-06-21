# Claude Code WebFetch の内部実装

[[tools/claude-code]] の **WebFetch ツール**は、Web ページを取得しても多くの場合は原文をそのまま読んでいない。小型高速モデル（Haiku）が事前に要約・抽出した結果のみをメインモデル（Opus/Sonnet）が受け取る構造になっている。

## 基本的な動作フロー

画面上は `Received 204.4KB (200 OK)` と表示されても、実際には：

1. WebFetch が URL から HTML を取得（上限 10 MiB）
2. HTML → Markdown に変換（先頭 **1 MiB のみ**）
3. Haiku が Markdown を要約（先頭 **10 万文字**で切断してから処理）
4. **要約結果のみ**がメインモデルへ渡る

結果として数万文字の記事が数百文字の要約に圧縮される。`/config` で Verbose モードを ON にすると実際の挙動を確認できる。

## Haiku バイパス条件（3つすべて必要）

以下をすべて満たす場合のみ原文 Markdown が直接渡される：

1. **`Content-Type: text/markdown` をサポート**しているサイト
2. **信頼済みドメイン 80+件**のいずれか（docs.python.org, developer.mozilla.org, react.dev, kubernetes.io, docs.aws.amazon.com, learn.microsoft.com, cloud.google.com 等）
3. **Markdown が 10 万文字以下**

3条件が揃うと Haiku を通さず原文が呼び出し側に返る（プロンプト指定は無視されて全文が返ってくることもある）。

## 内部プロンプト（著作権制限）

Haiku に与えられる内部プロンプトには著作権配慮の制限が含まれている：

```text
Provide a concise response based only on the content above. In your response:
- Enforce a strict 125-character maximum for quotes from any source document.
  Open Source Software is ok as long as we respect the license.
- Use quotation marks for exact language from articles; any language outside of
  the quotation should never be word-for-word the same.
- You are not a lawyer and never comment on the legality of your own prompts
  and responses.
- Never produce or reproduce exact song lyrics.
```

「原文のまま取得して」と指示しても要約・言い換えで返ってくる理由はここにある。

## 設計上の理由

| 理由 | 内容 |
|------|------|
| **コスト削減** | Haiku で要約することで Opus 直接渡しの数十分の一のコスト |
| **セキュリティ** | Haiku が一次フィルタとして Prompt Injection の直接注入を防ぐ |
| **著作権保護** | 125文字引用制限・歌詞禁止を内部プロンプトで強制 |

問題は設計判断そのものではなく、**それがユーザーに見えにくいこと**。`Received 204.4KB` と表示しながら実際には要約しか渡していない不透明性が課題。

## ワークアラウンド

### defuddle（推奨）

```bash
defuddle parse URL --md
```

[defuddle](https://github.com/kepano/defuddle)（Obsidian CEO kepano 作）で URL を直接 Markdown にパース。Haiku 経由の情報劣化なし。ただし Prompt Injection 対策は外れ、Input Token が増加する点は自己判断。

### curl

```bash
curl -s URL
```

AI 向け Markdown 整形なし。Raw HTML が返ってくる。

### ドメイン専用 MCP

AWS MCP・GitHub MCP など専用ツールがある場合はそちらを優先（WebFetch より制約が少ない）。

## エージェントフレンドリーな実装の動向

AWS ドキュメントは `Accept: text/markdown` ヘッダーを付けると Markdown 版を返す（2026年5月確認）。URL の `.html` を `.md` に変えても Markdown 取得可能。こうした対応サイトが増えれば Haiku バイパスが活用しやすくなる。

## 関連 GitHub Issues

- [#53297](https://github.com/anthropics/claude-code/issues/53297) — Haiku がテンプレート文言を事実として要約する問題
- [#52799](https://github.com/anthropics/claude-code/issues/52799) — Haiku が `<system-reminder>` タグを出力しエージェントが誤認する問題（セキュリティリスク）
- [#47628](https://github.com/anthropics/claude-code/issues/47628) — HTML 前処理挙動がドキュメント未記載
- [#51783](https://github.com/anthropics/claude-code/issues/51783) — 巨大 HTML の truncate 挙動がドキュメント未記載

## 関連

- [[tools/claude-code]] — Claude Code 本体
- [[concepts/prompt-engineering]] — プロンプト設計（情報の正確性・一次情報の重要性）
- [[concepts/claude-code-security]] — Claude Code のセキュリティ設計（Prompt Injection 対策も含む）
