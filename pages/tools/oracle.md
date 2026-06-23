# oracle

Codex CLI から OpenAI の最上位モデル **GPT-5.5 Pro** を呼び出すツール（steipete 製・GitHub `steipete/oracle`）。Codex の既定モデル [[models/gpt-5-5|GPT-5.5]] で日常の実装を回しつつ、戦略立案・記事構成・コードレビューといった「重い判断」だけを oracle 経由で GPT-5.5 Pro に投げ分ける使い方を想定する。

- **CLI から GPT-5.5 Pro を直接呼べる** —— 通常の Codex ワークフロー内に上位モデルを差し込める
- **容量カウントが別枠** —— Codex 本体とは消費枠が分かれ、実質2倍使えるとされる（伝聞・要一次確認）
- **重要工程に上位モデルを充てる** —— 戦略・記事構成・レビューなど質が効く工程を最上位モデルに任せる
- **Codex から全操作** —— Codex のモバイル操作と組み合わせれば、スマホからでも上位モデルを使った開発ができる

これは工程ごとにモデルを使い分ける [[concepts/llm-model-selection-strategy]]（上流＝大きいモデル／下流＝小さいモデルのサンドイッチ戦略）を、Codex CLI に統合したツールにあたる。「重要な脳」だけ上位モデルに任せる発想。

## 観察ログ（未検証）

- 2026-06-21: @ceo_tommy1 が「Codex から GPT-5.5 Pro に仕事させる方法が便利すぎて仕事が無限に捗る」「クオリティが上がりすぎてヤバい」と紹介（X bookmark 2,792 / 2026-06-23 時点）。容量2倍は「らしい」という伝聞で一次未確認

## 問い

- 容量カウント別＝実質2倍は本当か。Codex 本体の枠とどう分離されているか一次確認したい
- GPT-5.5 Pro を戦略/レビューに、GPT-5.5 を実装に、という分担は [[concepts/llm-model-selection-strategy]] のサンドイッチ戦略と同じ効果が出るか

## 関連

- [[tools/openai-codex]] — oracle の呼び出し元（Codex CLI）
- [[models/gpt-5-5]] — Codex 既定モデル。oracle はその上位 Pro 版を呼ぶ
- [[concepts/llm-model-selection-strategy]] — 工程分解型モデル選択（oracle はこれを CLI 統合した実装）
- [[tools/skill-cleaner]] — 同じ steipete 製の Codex/OpenClaw 向けツール
