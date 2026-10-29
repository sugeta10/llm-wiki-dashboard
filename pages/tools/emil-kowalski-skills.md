# Emil Kowalski skills（/apple-design ほかデザインエンジニア向けスキル集）

Emil Kowalski（VercelやLinearで働いた経験を持つデザインエンジニア・アニメーション講座 animations.dev 運営）が公開する、デザインエンジニア向けのエージェントスキル集（GitHub: emilkowalski/skills）。話題の中心は2026-07-09発表の **apple-design** スキルで、Kowalski氏は「AppleのWWDC動画は知識の金鉱。お気に入りの回を漁って17のデザイン・モーション原則にまとめた。既存作品のレビューにも、新しく作るときに正しく当てるためにも使える」と述べている。Appleがインターフェイス設計と流体モーションについて語ってきた原則を、Web向けに翻訳して蒸留したものと位置づけられる。

> 📌 X bookmark: 20,273（2026-07-11 時点）

## なぜ使うか——「エージェントには趣味（taste）がない」

Kowalski氏の問題意識は「Agents don't have great taste」。enterアニメーションに `ease-out` を使うべき場面で `ease-in` を選ぶ、半透明のshadowにすべきところでsolidなborderを選ぶ——こうした小さな取り違えが複利してインターフェイスを「素晴らしい」か「なんか惜しい」かに分ける、と氏は指摘する。スキルはエージェントが犯しうる小さな誤りを列挙し、直し方を説明する形式で書かれている（氏の記事「Agents with Taste」で解説）。「スロップの海で際立つためのショートカット」という売り文句が付く。

もう1つの主張は、これらのスキルがドメイン専門性の副産物だという点——「AIはそうした専門性を置き換えるのではなく、そこから引き出せるものを増幅し、他者に対して相対的にずっと有利にする。だからコードでもデザインでも、どの分野でも専門性を磨くことには極めて価値がある」とKowalski氏は述べる。スキル＝言語化された判断という [[business/skill-library-strategy]] の個人版とも読める。

## 収録スキル（2026-07-11時点）

| スキル | 概要 |
|---|---|
| `emil-design-eng` | メインスキル。アニメーション中心にデザイン全般の助言も含む |
| `review-animations` | Kowalski氏のルールに基づきアニメーションを厳格にレビュー |
| `animation-vocabulary` | 正しい語彙でAIに「欲しいアニメーション」を正確に伝えるための用語集 |
| `apple-design` | AppleのWWDCデザイントークから蒸留した17原則（インターフェイス設計・流体モーション）のWeb向け翻訳 |

インストールは `npx skills@latest add emilkowalski/skills`（skills.sh 経由）。

## 普及の節目と収録数の拡大（2026-10-26 時点）

Kowalski氏は 2026-10-26 に、リポジトリが **50,000 stars・270万ダウンロード**に達したと投稿し、「More skills coming soon, you'll like them」と次のスキルを予告した（ダウンロード数の集計元は投稿に記載がない）。wiki 側で 2026-10-29 に GitHub API を確認したところ、stars は 51,258、最終 push は 2026-10-28 で、`skills/` 配下のディレクトリは上の表の4本から16本に増えていた（animate・animate-expo・animation-performance・animation-vocabulary・apple-design・ask-sonner・break-ui・emil-design-eng・find-animation-opportunities・improve-animations・mobile-native・pick-ui-library・prototype・remove-animations・review-animations・write-swift）。ディレクトリ名から読む限り、Expo・Swift などモバイル向け、UI ライブラリの選定（pick-ui-library・ask-sonner）、アニメーションの発見・改善・削除・性能、プロトタイプ作成へ範囲が広がっていると考えられる。各スキルの SKILL.md 本体は未取得。

## 問い

- apple-designの17原則の中身は何か（SKILL.md本体は未取得）。[[tools/ui-skills]] 掲載の `emil-design-eng` と本リポは同一ソースか
- 16本に増えた現在、stars とダウンロードを牽引しているのはどのスキルか。skills.sh 側でスキル別のインストール数は取れるか。remove-animations（アニメーションを消す）を apple-design と並べて置く意図は何か
- review-animations（レビュー）とapple-design（原則）を制作フローのどこで使い分けるか。frontend-design系スキルと併用して衝突しないか

## 関連

- [[tools/ui-skills]] — デザインエンジニア向けスキルカタログサイト。`emil-design-eng` が掲載済み
- [[concepts/claude-skills]] — スキル＝永続的職務定義という土台概念
- [[design/design-md]] — 有名サービスのデザイン規範を.md化してエージェントに参照させる同系の「taste を参照仕様で渡す」発想
- [[companies/apple]] — 17原則の出所であるWWDCデザイントークの発信元
- [[design/ai-skills-design]] — AIエージェントのSkillsとデザイン職のスキルが交差する現状の論考
- [[tools/taste-skill]] — 同じ「agents don't have taste / anti-slop」問題意識のフロントエンド特化スキル集（ダイヤル調整・画像生成系統つき）
