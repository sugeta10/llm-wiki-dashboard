# Claude Fable 5.1 / Mythos 5.1

[[companies/anthropic]] 公式アカウント（@claudeai）が2026-09-01に発表した [[models/claude-fable-5|Fable 5 / Mythos 5]] の後継バージョン。公式は Fable 5.1 と Mythos 5.1 を同時に紹介し、「コーディングと知識労働のための世界で最も先進的なモデル」と位置づけている。

発表ポストで捕捉できたのはこの一文と動画リンクのみで、価格・ベンチマーク・提供範囲・Fable 5 からの変更点はまだ本vaultに未収集。具体的な仕様は Anthropic 公式ブログやシステムカードなど一次ソースの追加を待つ。

Fable と Mythos を対で発表する形は、Fable 5 / Mythos 5 のときの「同一基盤モデルでセーフガードの有無だけが違う」二本立てをそのまま引き継いだものと考えられる（発表ポストには明記されていないため未確認）。

## 第三者による評価

- @Charlie_no_siteは、Anthropicの5.1向け公式プロンプトガイドに推奨プロンプトが14本（開発者向け7本・業務向け7本）あり、業務向けはすべて仕事の中身でなく進め方だけを指定していると整理した。途中経過を出さない・途中で止まる・原文をそのまま写す（5.1で増えたと公式が明記、と著者）などの癖への対処が並ぶ。詳細は[[concepts/fable-5-1-prompting]]
- @yugen_matuniは、Fable 5.1の内容の正確さと論理の深さは申し分ないが日本語出力には翻訳調や堅い言い回しが出やすく、自作の日本語検査Hook（500本超）で十数件の警告が出るのが日常だったと述べる。推論力と日本語の自然さは別能力だという主張は[[concepts/reasoning-vs-japanese-fluency]]
- @Voxyz_aiは、Fable 5.1がクォータを早く使い切りすぎるときは実装から外し、[[tools/herdr]]のマルチペイン構成でコーディネーター（方向づけ・タスク分解・最終サインオフ）に回すと述べる。実装はClaude・Codexの各ペインが担う

## 問い

- Fable 5 から 5.1 で変わったのは何か（性能・価格・セーフガードの誤発火率・提供プラン）。公式ブログで確認する
- Fable 5 向けに確立したプロンプト指針（[[concepts/fable-5-prompting]]）やループ設計はそのまま通用するか。[[concepts/instruction-patch-lifecycle]] の「モデル世代が変わったら指示を外して試す」手順を回す機会か。5.1向け公式ガイドの業務向け7本は[[concepts/fable-5-1-prompting]]に整理済みで、公式ページ本体のingestが次の一手
- Fable 5 に近い性能を半額で提供するとされる [[models/claude-opus-5]] との棲み分けはどう変わるか

## 関連

- [[companies/anthropic]] — 開発元
- [[models/claude-fable-5]] — 前バージョン。Mythos クラス初の一般公開・価格・セーフガード設計・プラン別提供条件の詳細はこちら
- [[models/claude-mythos]] — Mythos 系列の起点（Mythos Preview・一般公開見送り）
- [[models/claude-opus-5]] — 同社の別ライン。Fable 5 のフロンティア知能に近い性能を半額で提供すると公式が位置づける
- [[concepts/fable-5-1-prompting]] — 5.1向け公式プロンプトガイドの業務向け7本を@Charlie_no_siteが日本語で整理したもの
- [[concepts/reasoning-vs-japanese-fluency]] — 推論には長けるが日本語出力は翻訳調が出やすい、という@yugen_matuniの第三者評価
- [[tools/herdr]] — クォータ消費が速い5.1を司令塔に限定し、実装を他エージェントのペインへ回す運用（@Voxyz_ai）
