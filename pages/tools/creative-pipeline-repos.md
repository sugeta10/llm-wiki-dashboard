# コーディングエージェントの制作工程 10 リポジトリ（Polish → Motion → 3D → Visuals → Captions → Export）

> **TL;DR**: Florence（@flxrnc）が「Fable 5.2 に必要な GitHub リポはこの 10 本だけ」と並べた一覧。磨き・動き・3D・画像・字幕・書き出しの 6 工程の順に Impeccable・GSAP Skills・Three.js・Blender MCP・anatomy・ComfyUI・faster-whisper・Remotion・HyperFrames・FFmpeg を置く。エージェント向けに作られたものは半分で、残りは汎用の定番ツールである。

投稿者の Florence はプロフィールに「AI・暗号資産・その日に起きたことを投稿する」とある個人アカウントで、映像制作者やツール作者としての肩書は書かれていない。投稿は 602 件ブックマークされた（2026-11-17 時点）。一覧の見どころは個々のリポの新しさではなく、「磨く→動かす→3D→画像を作る→字幕→書き出す」という制作工程の順に 1 本ずつ当てはめた並べ方にある。末尾の「guide below」は同じ投稿者の X 記事「The Fable 5.2 Motion Playbook: One Prompt, One Film」（40 本の完成作から得た 9 教訓）を指し、この一覧はその記事の道具箱という位置づけになっている。

## 10 本の一覧（投稿の順）

投稿者の一言は投稿からの訳、description 以降は GitHub API の記載（2026-11-18 取得）。

| # | リポジトリ | 投稿者の一言 | GitHub の description | owner・license | stars |
|---|---|---|---|---|---|
| 1 | [pbakaus/impeccable](https://github.com/pbakaus/impeccable) | 「AI っぽい」を「誰がデザインした？」に変える | コーディングエージェントのデザイン語彙。polish・bolder で操作し critique・audit でレビューし、出荷前に AI スロップを捕まえる | 個人（Paul Bakaus）・Apache-2.0 | 112,152 |
| 2 | [greensock/gsap-skills](https://github.com/greensock/gsap-skills) | GSAP チーム直伝で、エージェントに滑らかなアニメーションを教える | GSAP 公式の AI スキル。AI コーディングエージェントに GSAP の正しい使い方・ベストプラクティス・定番パターン・プラグインを教える | 組織（greensock）・MIT | 18,637 |
| 3 | [mrdoob/three.js](https://github.com/mrdoob/three.js) | ブラウザでそのまま動く 3D シーン | JavaScript の 3D ライブラリ | 個人（mrdoob）・MIT | 118,764 |
| 4 | [ahujasid/mcp-for-blender](https://github.com/ahujasid/mcp-for-blender) | エージェントが Blender の中で手を動かせる | 任意の LLM で Blender 3D を操作するコミュニティプラグイン。Blender Foundation 公式とは無関係 | 個人（ahujasid）・MIT | 34,811 |
| 5 | [wheresryan22/anatomy](https://github.com/wheresryan22/anatomy) | どんな機械でも分解して仕組みを見せる | 実在の機械や複雑なシステムの詳細なインタラクティブ等角投影イラストを作るエージェントスキル。SVG を生成し、WebGL シェーダと 3D 効果は任意 | 個人（wheresryan22）・MIT | 27,862 |
| 6 | [Comfy-Org/ComfyUI](https://github.com/Comfy-Org/ComfyUI) | 自分のマシンで AI 画像と動画を作る | グラフ／ノード式の拡散モデル GUI・API・バックエンド | 組織（Comfy-Org）・GPL-3.0 | 147,351 |
| 7 | [SYSTRAN/faster-whisper](https://github.com/SYSTRAN/faster-whisper) | どんな声からも字幕を、Whisper よりずっと速く | CTranslate2 による高速 Whisper 文字起こし | 組織（SYSTRAN）・MIT | 26,835 |
| 8 | [remotion-dev/remotion](https://github.com/remotion-dev/remotion) | コードから動画を丸ごと組む | React でプログラム的に動画を作る | 組織（remotion-dev）・独自ライセンス | 73,892 |
| 9 | [heygen-com/hyperframes](https://github.com/heygen-com/hyperframes) | HeyGen の無料ツール。Web ページを動画にする | HTML を書けば動画になる。エージェント向けに作られた | 組織（heygen-com）・Apache-2.0 | 85,955 |
| 10 | [FFmpeg/FFmpeg](https://github.com/FFmpeg/FFmpeg) | 最終アニメーションの書き出し | git.ffmpeg.org のミラー | 組織（FFmpeg）・LGPL/GPL | 67,615 |

投稿は 6 工程と 10 本の対応を 1 対 1 では書いていない。並び順から読むと、Impeccable が Polish、GSAP Skills が Motion、Three.js と Blender MCP が 3D、anatomy と ComfyUI が Visuals、faster-whisper が Captions、Remotion・HyperFrames・FFmpeg が Export にあたると考えられる（wiki 側の推論）。

## 投稿者の一言と GitHub 記載の照合

- **エージェント向けに作られたのは 5 本**。Impeccable（スキル集）・GSAP Skills（公式 AI スキル）・Blender MCP（MCP サーバ）・anatomy（エージェントスキル）・HyperFrames（「Built for agents」）は description 自体がエージェントからの利用を前提にしている。Three.js・ComfyUI・faster-whisper・FFmpeg は人が使う汎用のライブラリとアプリで、Remotion も React の動画フレームワークである。「Fable 5.2 に必要なのはこれだけ」という書き方は投稿の宣伝的な枠で、どのリポの description にも Fable 5.2 への言及はない。
- **GSAP Skills と HyperFrames の出所は一致する**。前者は greensock 組織の配下で description に「Official」とあり、後者は heygen-com 組織の配下で Apache-2.0。投稿者の「GSAP チーム直伝」「HeyGen の無料ツール」はリポ側の記載と合う。
- **Blender MCP は非公式**。投稿者は「エージェントが Blender の中で手を動かす」とだけ書くが、リポの description は「Blender Foundation 公式とは無関係のコミュニティプラグイン」と明記している。
- **更新の鮮度に差がある**。GSAP Skills の最終 push は 2026-07-29、anatomy は 2026-10-18 で、他の 8 本（2026-11-12 から 17 の間）より間が空いている。

## 引用先の記事「The Fable 5.2 Motion Playbook」

投稿が「guide below」として引用する同じ投稿者の X 記事は、Fable 5.2 で作った 40 本の完成作から 9 教訓をまとめたもので、本文はキャッシュした（コードブロックと画像は未収録）。記事冒頭の要約から 9 教訓の見出しだけ転記する。

1. Effort は Max でなく High（Max は考える時間が長く、費用は約 3 倍で、確実に良いわけではない、と投稿者）
2. 形容詞を数字に置き換えるほど映像は良くなる
3. 着地の感触はスプリングの減衰比という 1 つの数で決まる
4. 静止画で検査するモデルは止まった映像を通してしまう。動きを測らせる（ffmpeg 1 行の凍結検出と Python 10 行の動きグラフ）
5. 先にタイミングを書き、「完成」の定義を渡す
6. Fable はルールに従いすぎるので、少なく良いルールを書く
7. Blender でマスコットの衣装をスクリプトで作る。モデルだけでなくヘルパー・制限・シートを頼む
8. エージェントチームは、作る側とレビューする側を分ける
9. 制限と費用。Medium で反復し High で本番を作る

記事本体は独立したページに値する分量で、本ページでは道具箱との関係を示す範囲に留めた。コードから動画を作る同系の講座 [[concepts/opus-motion-design-studio]] と重なる論点（スプリング・動きの検査・ffmpeg）が多く、取り込むなら差分で書くのがよいと考えられる。

## 既存ページとの関係（wiki 側の推論）

- 10 本のうち wiki に独立ページがあるのは [[tools/impeccable]]・[[tools/remotion]]・[[tools/hyperframes]] の 3 本。three.js は素材側の [[tools/threejs-assets]]、FFmpeg はスキル側の [[tools/ffmpeg-skill]] がある
- Remotion と HyperFrames を両方レンダラとして組み込む [[tools/openmontage]] は、この一覧の Export 側を 1 つのシステムにした例にあたる
- 同じ「リポ N 選」の投稿形式では [[tools/agent-skills-by-role]] が職種別、本ページが工程別。UI 専門のカタログ [[tools/ui-skills]] には Impeccable が掲載されている

## 観察ログ（未検証）

- 2026-11-18: 投稿者の記事は「Max は High の約 3 倍の費用で、確実に良いわけではない」と書く。40 本の自作からの単一ソースで、設定や計測法はキャッシュした記事本文で確認できる範囲に限る

## 問い

- 独立ページがない 6 本（GSAP Skills・three.js 本体・Blender MCP・anatomy・ComfyUI・faster-whisper）のうち、エージェント用途で README を読む価値が高いのはどれか。GSAP Skills と anatomy は description がスキルそのものを指す
- 投稿者の Playbook の 9 教訓は [[concepts/opus-motion-design-studio]] の 12 ステップとどこが違うか。両方が ffmpeg による動きの検査を置いているなら、検査の実装は同じか

## 関連

- [[tools/impeccable]] — 一覧の 1 本目。Paul Bakaus のデザイン語彙スキル集
- [[tools/remotion]] — 一覧の 8 本目。React で動画を組むフレームワーク
- [[tools/hyperframes]] — 一覧の 9 本目。HTML から動画を描く HeyGen 製フレームワーク
- [[tools/threejs-assets]] — 3 本目 three.js 向けの無料 GLB モデル群
- [[tools/ffmpeg-skill]] — 10 本目 FFmpeg をエージェントから自然言語で使わせるスキル
- [[tools/openmontage]] — Remotion と HyperFrames をレンダラに持つエージェント型動画制作システム
- [[concepts/opus-motion-design-studio]] — コードから動画を作る講座。引用記事の Playbook と論点が重なる
- [[tools/ui-skills]] — Impeccable を掲載する UI スキルのカタログ
- [[tools/agent-skills-by-role]] — 職種別のリポ 6 選。本ページは工程別
