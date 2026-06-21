# Hyperframes

> **TL;DR**: HTMLに`data-*`属性でタイミング情報を付けるだけで決定論的にMP4を出力する、HeyGen製OSS（Apache 2.0）の動画レンダリングフレームワーク。「LLMの母語＝Web」を動画タイムラインに変換することで、AIエージェントが人手介入なしに動画を作れるようにした。

After EffectsやDaVinciがタイムライン・レイヤー・キーフレームをXML/JSON/バイナリで持つのは「人間が視覚的に配置する」前提の設計で、LLMには学習サンプルが乏しく外国語に等しかった。Hyperframesはこの障壁を、LLMが圧倒的な学習量を持つHTML/CSS/JS（GSAP・Lottie・Three.js等のWeb資産がそのまま使える）に動画の状態を載せ替えることで解く。AIにとって「書き慣れた言語で動画を書く」状況を作り出した点が本質で、土台が整ったのは2025年11月の[[models/claude-opus-4-8|Claude Opus 4.5]]/Gemini 3世代でコード生成が「動画として成立する品質」を超えた瞬間だとされる。

「AIにGUIを直接触らせず、LLMが得意なテキスト表現を中間層に挟む」という構図は [[concepts/intermediate-notation-pattern]]（MNP）と同型で、HyperframesはHTML+`data-*`属性をその中間記法として採用した実例と読める。

## 仕組み

- **タイミング記述**: `data-composition-id`（識別子）・`data-start`（開始秒）・`data-duration`（表示秒）・`data-track-index`（レイヤー重なり順）をHTML要素に付与。GSAPのタイムラインを`window.__timelines[id]`に登録してアニメーションを定義する。
- **決定論レンダリング**: 現在時刻ではなく`frame = floor(time × fps)`のフレーム番号を絶対基準にする。ヘッドレスChromeの`beginFrame` APIで1フレームずつキャプチャ→FFmpegで圧縮。同一HTMLからどのマシンでも100%同一のMP4が出るため、CI/CDでの動画自動生成に向く。
- **必要環境**: Node.js 22+・FFmpeg・[[tools/claude-code]]の3つ（Dockerは任意）。

## 主なCLI操作

| コマンド | 役割 |
|---------|------|
| `npx skills add heygen-com/hyperframes` | Claude Codeに使い方スキル（`/hyperframes`・`/hyperframes-cli`・`/gsap`）を追加 |
| `npx hyperframes init <name> [--example <tpl>]` | プロジェクト初期化（`meta.json`/`index.html`/`compositions/`/`assets/`）。`--video`指定で文字起こし字幕テンプレ生成 |
| `npx hyperframes preview` | ブラウザにHyperframes Studio（プレビュー+タイムライン）を起動、保存即リロード |
| `npx hyperframes add <block>` | 42種以上のビルトインブロックを`compositions/`に投入 |
| `npx hyperframes lint` | 構造エラーを特定（長大ログをLLMに貼る前にこれを実行するのが推奨） |
| `npx hyperframes render --output <file>.mp4` | MP4書き出し（デフォルト1920×1080/30fps） |

## テンプレート・ブロック

- **スタータープロジェクト9種**: 16:9が7種（warm-grain/play-mode/swiss-grid/kinetic-type/decision-tree/product-promo/nyt-graph）、9:16が1種（vignelli）、白紙のblank。
- **ブロック42種以上**: SNSオーバーレイ7種（Instagram/TikTok Follow・YouTube Lower Third・Spotify Now Playing・X/Reddit Post Card・macOS Notification）、シェーダートランジション14種、CSSトランジション13カテゴリ、エフェクト3種。Web上のCodePen/Dribbble等の作例を「コピペ＋タイミング付与」で動画素材化できる。

## エージェント前提の設計（vs Remotion）

- CLIがデフォルト**非対話モード**で、フラグを渡せばAIが人間の介入なしに完走できる。
- 素のHTMLのみで書ける（RemotionはReact必須＝コードが厚くLLMが間違えやすい）。
- Apache 2.0で商用無制限（Remotionは規模次第で商用有料ライセンス）。
- 「人間のReactエンジニアが自分で書くならRemotion、AIに書かせるならHyperframes」が現時点の整理。

## プロンプト4型（公式推奨）

1. **Cold start**: ゼロから指示（尺・アスペクト比・雰囲気・要素を併記すると精度向上）
2. **Warm start**: PDF/GitHub/CSV等の素材を渡して要約しつつ動画化（公式は「出力品質が高い」と明記）
3. **Format-specific**: TikTok/YouTubeショート等のフォーマット先指定
4. **Iterate**: 既存動画に対話で修正を重ねる（Claudeが文脈を保持）

## HeyGenの戦略的意図

HeyGenの本業はAIアバター動画だが「アバターは動画の半分」であり、周囲のモーショングラフィックス制作がユーザーの詰まりポイントだった。その制作層をOSSで配布してコミュニティに磨かせ、自社有料プロダクト（Video Agent）では磨かれたHyperframes＋アバターの両輪を提供する青写真。「HTMLで動画を書く」標準を自社が握る狙いで、Linux/Kubernetes/Reactの「OSSで覇権を取る」パターンの動画版と位置づけられる。

## 観察ログ（未検証）

- 2026-04-16: HeyGenのVP of Product & Agent Engineering Bin Liu（旧Alisa共同創業者、2025年9月HeyGenに買収）がGitHubで公開。著者（@MakeAI_CEO）は「リリース24時間でX上が祭り状態」と記述（単一ソースの主張）。
- ローンチPR動画自体がClaude Code × Hyperframesのみで制作され、ソースコードを「RT＋コメントで配布」する自己言及的プロモを行ったとされる。
- 著者主張: モーショングラフィックス外注は従来「1本5〜10万円・納期2週間」が相場 → Claude Code環境で「コスト0円・納期10分」に近づく。発信者の活用例8選（X記事冒頭モーション量産・noteヘッダー動画・VSL冒頭15秒・PDF→45秒ピッチ・CSV→アニメチャート・LINEティザー・多言語展開・ショート動画量産）を提示。いずれも効果測定は未検証。
- 著者の煽り文脈（「今が一番早い」「Rails/Next.js黎明期」類比）はポジショントークの可能性に留意。

## 検証済み事実

- ライセンスはApache 2.0（商用無制限）。
- レンダリングは決定論的（フレーム番号基準・ヘッドレスChrome `beginFrame`＋FFmpeg）で、同一入力から同一MP4を再現する設計。

## 問い

- 決定論レンダリングを使い、note/wiki記事公開をトリガーにヘッダー動画を自動生成するCI/CDを自分のvaultで組めるか（[[concepts/llm-wiki]]連携）。
- `data-*`属性ベースの中間記法は [[concepts/intermediate-notation-pattern]] のserialize/parse対と比べてどこまで双方向編集できるのか。
- Claude Opus 4.7/4.8世代で実際にどの程度安定して「動画として成立する」HTMLを出せるか、blank/play-modeで1本試す。

## 関連

- [[tools/claude-code]]（動画生成のエージェント実行環境・`/hyperframes`スキル）
- [[models/claude-opus-4-7]]（Hyperframesが想定する実用モデル。転換点はOpus 4.5世代）
- [[concepts/intermediate-notation-pattern]]（AIにDSL/中間記法を操作させる設計思想の同型例）
- [[tools/remotion]]（同じくコードから決定論的に動画生成するFW）
- [[business/youtube-shorts-jidouka-marketing]]（動画量産マーケのレンダリング層として利用）
- [[companies/anthropic]]
