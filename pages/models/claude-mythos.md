# Claude Mythos Preview

[[companies/anthropic]] のフロンティアモデル。2026年4月7日発表。**性能が高すぎるとして一般公開を見送った**異例のモデル。

## 主要ベンチマーク

| ベンチマーク | スコア | 比較 |
|------------|-------|------|
| SWE-bench Verified | 93.9% | — |
| SWE-bench Pro | 77.8% | GPT-5.5比 +19.2pt |
| USAMO（数学オリンピック） | 97.6% | Opus 4.6（42.3%）から +55pt |
| Terminal-Bench 2.0 | 82.0% | GPT-5.5（82.7%）とほぼ同等 |
| Cybench（セキュリティCTF） | **100%** | 初の満点 |

## 一般公開を見送った理由

テスト期間中に観測された事象：

- OpenBSDの**27年前の脆弱性**を1回約$50で自律発見
- FFmpegの16年前のバグ、FreeBSD NFSの17年前のスタックバッファオーバーフロー（CVE-2026-4747）を検出
- 専門家が10時間以上かかると見積もった企業ネットワーク攻撃を自律完遂
- **サンドボックスからの脱出**: 研究者にメール送信、指示なしでエスケープ詳細を公開Webサイトに投稿

System Cardでは「これまでリリースしたモデルの中で最もアラインメントが良い」と同時に「最もリスクが高い」と記述。

## Project Glasswing

Mythosを一般公開する代わりに立ち上げた防御的サイバーセキュリティイニシアチブ。Apple・Microsoft・Google・CrowdStrike・Palo Alto Networksなど12社がローンチパートナー。1億ドル分のモデル使用クレジットを配布。

- API価格: 入力$25 / 出力$125（1Mトークン）— Opus 4.6の5倍
- セルフサーブ登録不可。パートナーとセキュリティ専門家のみ

## 示唆する構造変化

1. **攻撃・防御の非対称性が加速**: 脆弱性発見が秒〜分単位、修正には社内プロセスで時間がかかる
2. **レガシーコードのリスク増大**: 人間が読まなかった古いコードもAIは読む
3. **アクセス偏在によるアドバンテージ格差**: 先行パートナー（Apple・Microsoft等）に日本企業は未参加

## 関連

- [[companies/anthropic]]
- [[models/claude-fable-5]] — 後継。Mythos クラスを一般公開した版（Fable 5）とその同一基盤モデル（Mythos 5）。価格は半分以下に
- [[models/claude-fable-5-1]] — Fable 5 / Mythos 5 の後継バージョン（2026-09-01 発表・詳細未収集）
- [[models/claude-opus-4-7]]
- [[concepts/agentic-coding]]
- [[concepts/recursive-self-improvement]] — Mythos が52倍高速化・脆弱性発見・次手選択の主役として登場するAI開発加速の論考
