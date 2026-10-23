# code-modernization｜レガシーコードを新実装へ置き換える Anthropic の Claude Code プラグイン

@dani_avila7 の投稿によると、Anthropic が Claude Code プラグイン（[[tools/claude-code-plugins]] と同じ配布形式の拡張）`code-modernization` の v1.0.0 を公開した。用途はレガシーコードベースのモダナイゼーション（古い言語・フレームワークで書かれたコードを現行の技術で書き直すこと）である。@dani_avila7 が注目点として挙げるのは、コードを書き直すだけで終わらせず、新しい実装が旧実装と同じように振る舞うことを示そうとする点だ。投稿本文はその説明の途中（「behaves like」の直後）で途切れており、等価性をどう確かめるのか（テストの自動生成か、新旧を同じ入力で走らせた出力比較か）、インストール方法、対応言語は未収集。配布元のマーケットプレイスが `claude-plugins-official` かどうかも未確認である。

書き直しの正しさを「新旧で振る舞いが一致すること」で定義する発想は、[[tools/claude-code-plugin-eval]] がプラグインあり/なしで同じテストを走らせ差分を見る構造と近いと考えられる（wiki 側の推論）。どちらも比較対象を手元に残し、差分を採点の根拠にする。また [[models/claude-mythos]] のページが挙げる「人間が読まなかった古いコードもAIは読む」というレガシーコードのリスクに対しては、読むだけでなく置き換えまで AI に任せる道具として位置づけられる（wiki 側の推論）。

## 問い

- 公式リポジトリで、振る舞いの一致を何で示すのか（特性テスト＝現状の挙動を固定するテストの生成か、新旧の出力差分か）を確かめる
- 一致を示せない部分（外部 I/O・時刻・乱数に依存する処理）をどう扱うのか
- このwikiの scripts/ のような小さな Python 群でも使い道があるか、それとも大規模な業務システム向けか

## 関連

- [[tools/claude-code-plugins]] — Claude Code プラグインの公式マーケットプレイス一覧。本プラグインの配布形式
- [[tools/claude-code-plugin-eval]] — あり/なしで同じテストを走らせ差分を見る公式コマンド。新旧比較で正しさを示す発想が近い
- [[models/claude-mythos]] — AI がレガシーコードを読むことで生じるリスクを指摘したページ
- [[tools/turn-inspector]] — 同じ @dani_avila7 が公開した Claude Code Mod
- [[concepts/cto-ciso-vuln-surge-playbook]] — 米内貴志（GMO Flatt Security 取締役 Co-CTO）が CTO/CISO に勧める「Rebuild and Retire」の経営判断側。本プラグインはその Rebuild をコードレベルで担う道具に当たる
