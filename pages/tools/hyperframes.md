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

## ブランド一貫性の仕込み：DESIGN.md / FRAME.md

発信者向けにHyperframesを解説した@MakeAI_CEOによれば、AI生成動画がテンプレ臭く見える原因は品質でなく「ブランド不在」で、色・フォント・トーンを渡さないままだとエージェントは毎回「それっぽい世界平均」を出してしまうという。この対策として2つのファイルを使う。1つ目のDESIGN.mdはブランドの色・フォント・言葉のトーンを記述したファイルで、AIデザインツール界隈で共通の慣習になりつつある形式だと同氏は説明する（公開ギャラリーも存在し、動画以外のAI制作物にも流用できる汎用資産だとする）。2つ目のFRAME.mdはDESIGN.mdに要素の登場・退場・場面転換といったモーションの言語を加えたファイルで、公式サイト（hyperframes.dev/design）にDESIGN.mdをアップロードすると変換される。FRAME.mdを動画プロジェクトのフォルダに置くだけで、そのフォルダで作る動画すべてにブランドのルールが自動適用される。

DESIGN.mdは自分のサイトのライブHTML/CSSからエージェントに抽出させるのが早いとされ、同氏が公開した抽出プロンプトは「色とフォントはCSSから一字一句そのまま抜き出す」「スクリーンショットからの色推測は禁止」「出典を示せない項目は削除する」という制約を課している点が特徴で、AIの推測によるハルシネーションを構造的に防ぐ設計だと同氏は評している。

## 5つの実践レシピ（公式ワークフロー）

@MakeAI_CEOが紹介するHeyGen公式の事例ベースで、Hyperframesには用途別のスキル（スラッシュコマンド）が同梱されている。入り口の`/hyperframes`が依頼内容に応じて以下へ振り分ける。

| コマンド | 入力 | 用途 |
|---|---|---|
| `/faceless-explainer` | テーマ | 顔出し・撮影・素材なしで解説動画を一括生成（リサーチ〜台本〜ナレーション〜図解〜字幕〜BGMまで） |
| `/music-to-video` | 音楽ファイル | BPM・拍・盛り上がりを解析しビートに同期したカット割りの動画を生成 |
| `/motion-graphics` | 数字・要点 | 10秒以下のナレーションなし短尺（カウントアップ・グラフ・ロゴ演出） |
| `/talking-head-recut` | 喋り動画 | 発言内容に同期した図解カード・テロップを自動で重畳 |
| `/pr-to-video` | GitHub PRのURL | タイトル・差分・コミット履歴を読み込みレビュアー向けブリーフィング動画を生成 |

HeyGenの公開事例では、`/faceless-explainer`がGitHub APIを自分で叩いて実在の人気プロジェクト（★25万超）を題材に選び台本を書いたケースや、`/motion-graphics`がGitHubのスター数をその場でAPI取得しカウントアップ演出に反映したケースが紹介されており、画面に出る数字が実データである点を@MakeAI_CEOは信頼性面の利点として強調している。

## スキル刷新と無料素材（2026-11・公式告知）

HeyGenのHyperFrames公式アカウント（@HyperFrames_）は2026-11-05（日本時間）に「OSS skillsを大幅に刷新した」と告知した。告知の内容は3点で、更新コマンドが `npx hyperframes skills update` であること、更新すると `/reverse-any-video` と「Proで最上位の生成モデル」が解放されること、サインインすれば10,000曲超の音楽・効果音・75,000点超のストック画像・TTS（音声合成）が無料で使えること。@tetumemoはこれを引用して「これが無料って凄い」と述べ、TTSの無料枠は月3分までと補足し、この更新をMuse（AIアシスタント）に投げて解説動画を作らせたと書く。月3分の数字は公式ポストには無いが、`/media-use` スキル本文に「Free plan: 3 min a month」と明記されている（後述）。

GitHubのリポジトリ説明は「AI video you can actually edit. HyperFrames is a free, open-source AI video editor.」となっており、「動画レンダリングフレームワーク」から「編集できるAI動画エディタ」へ自己定義を広げている。README（2026-11-19取得）によれば、HyperFrames Studioアプリも無料で、Proプランが「HeyGenの最上位のvideo・image・voice・musicモデル」を追加する。Studioにはデスクトップ版（macOS・Windows・Linux）がある。料金ページはJavaScript描画のため本wikiでは未取得。

### 公開スキル9本と既定で入るcore set 6本（README）

| 区分 | スキル | 用途（READMEの記述） |
|---|---|---|
| core | `/hyperframes` | 最初に読む。レンダリングの契約と作業ループを持ち、format・motion・look・captions・全編・ストーリーボードの参照先を指す |
| core | `/hyperframes-cli` | CLIを仕事別に整理（scaffold・check・フレーム確認・timeline編集・preview・ローカル／クラウドrender・share・履歴）。JSONの読み方とビルド／レンダ失敗からの復旧 |
| core | `/hyperframes-registry` | 約450のブロック・コンポーネント（transition・overlay・title・kinetic type・captions・chart・map・3D・camera move）を編集可能なソースとして導入 |
| core | `/media-use` | 音楽・効果音・画像・アイコン・ロゴ・ナレーション・アバター・生成動画・文字起こし・切り抜き・グレーディングをHeyGenか無料ローカルツールで取得・生成し、配置した音声をミックス |
| core | `/reverse-any-video` | 任意の動画ファイルを編集可能なHyperFramesプロジェクトに再構築し、原本とフレーム単位で照合。**HyperFrames Pro** |
| core | `/style-sampling` | 5つの実証済みスタイル（Collage Explainer・Finance Explainer・Animated Science Explainer・3D Brand Explainer・3D True Crime）で新しいエピソードを作る。**HyperFrames Pro** |
| on demand | `/figma` | figma.comのリンクを貼ったとき。レンダ済み素材・ブランドトークン・コンポーネント・ストーリーボードを動画へ |
| on demand | `/slideshow` | プレゼン・ピッチデッキ・インタラクティブデッキ。出力はMP4でなくナビゲーションできるデッキ |
| on demand | `/remotion-to-hyperframes` | 既存の動画コードや映像（Remotion優先・Motion Canvas・Lottie・Three.js・canvas等）をHyperFramesへ移し、フレーム単位で検証 |

インストール経路の規則（README）:

- `npx hyperframes skills update` は、CLIのバージョンに一致するリリースタグからcore setを入れる。`npx skills add` が参照するskills.shレジストリはmainから数時間遅れることがある、とREADMEが使い分けの理由を書く。既存スキルは更新するだけで削除しない。on demandの3本は `npx hyperframes skills update <name>` で個別に足す。
- `npx skills add heygen-com/hyperframes` のピッカーは対話専用で、何も選ばれていない状態で開く。スクリプトやエージェントからの非対話実行で `--skill` を付けないと9本すべてが入る。`--all` は意図的に全部入れる指定。
- `npx hyperframes init` はcore setが無いときだけ入れ、既存スキルには触らない。
- Claude Codeには `claude plugin marketplace add heygen-com/hyperframes` → `claude plugin install hyperframes@hyperframes` のversioned plugin経路もあり、呼び出しは `/hyperframes:hyperframes`。

**本ページの旧記述との差分**: 上の「主なCLI操作」表にある `/gsap` と、「5つの実践レシピ」の `/faceless-explainer` など5コマンドは、2026-11-19時点のREADMEが挙げる公開9本に載っていない。READMEは「`.claude/skills` / `.agents/skills` 配下のリポジトリ内部スキル6本は既定で除外」とも書くので、改名・統合・内部化のいずれかと考えられるが、対応関係は未確認。

### `/media-use` の供給元（SKILL.mdの表を転記）

| 必要なもの | 無料 | HyperFramesクレジット | ローカル／本人のキー（求められたときだけ） |
|---|---|---|---|
| 音楽（bgm） | HeyGenカタログ10k+曲（サインイン） | 生成トラック | ローカルMusicGen |
| 効果音（sfx） | HeyGen（サインイン）・Openverse（CC BY表記）・同梱 | | |
| 画像・アイコン | HeyGen素材検索75k+（サインイン） | 生成画像 | ローカルFLUX（mflux、導入済みなら） |
| ロゴ（logo） | theSVG・GitHubアバター・favicon（描き直さない） | | |
| ナレーション（voice） | Voice Turbo・Freeプランは月3分 | 有料プランのVoice Turbo・他のHeyGen音声・クローン | `npx hyperframes tts`（端末上のKokoro） |
| 話す登壇者 | | アバター動画（`--type video`） | ローカルLTX（汎用クリップ、導入済みなら） |
| 文字起こし | `npx hyperframes transcribe`（Parakeetかwhisper） | | |
| 切り抜き | | 動画: `--type cutout` | `npx hyperframes remove-background` |
| グレード・LUT・ルック | `npx hyperframes media-treatment` | | |

スキル本文はエージェントの振る舞いも規定する。HeyGenのモデルやカタログが要る依頼では毎回 `npx hyperframes auth status` を先に実行し、未サインインなら「サインインは無料で、音楽カタログ・素材検索・FreeプランのVoice Turbo月3分が使える」と一行で伝えて `npx hyperframes auth login` を促す。ローカルツールは本人が求めたときだけ使い、フォールバックにはしない。クレジットの既定は「頼まれない限り安い代替や項目の削除をしない」で、本人が `.hyperframes/credit-limit.json` に上限を書くと超える呼び出しは拒否される。素材はすべてプロジェクトの `assets/` に台帳付きで置かれ、レンダリング時に外部取得は走らない。

OSSとして無料で配る部分（決定論レンダリング・ブロック・スキル本体）と、サインインやProに寄せる部分（素材カタログ・生成モデル・`/reverse-any-video`・`/style-sampling`）の線引きは、「HeyGenの戦略的意図」の節で書いたOSSで標準を取り有料側で回収する構図の具体化と考えられる（wiki側の読み）。

## ターミナル不要の入り口：Video Agent

CLIに抵抗がある層向けに、HeyGen本体（app.heygen.com）にはVideo Agentというブラウザ完結の機能が組み込まれている。作りたい動画を言葉で説明すると、エージェントがシーンごとの制作プランを先に提示し、対話で調整してから初めてレンダリングが走る。プロンプトには「スタイル段落」と呼ばれる5〜6文の型（スタイル名・hexコード込みのパレット・動き方・場面転換・雰囲気）を添えるのが公式推奨とされる。レンダリング後もチャットが継続し、気になるシーンだけを指定して直せるほか、AI Studioへコピーを送ると台本・シーン・声・タイミング・素材を人手で微調整する完全手動モードに切り替えられる。@MakeAI_CEOはこの運用を「9割をプロンプトで、1割を編集で」と整理し、無料量産やブランドのファイル管理を厳密にしたいならターミナル版、アバターに喋らせたい・黒い画面を避けたいならVideo Agentという使い分けを提案している（Video AgentはHeyGen本体機能のためアカウント登録が必要）。

## HeyGenの戦略的意図

HeyGenの本業はAIアバター動画だが「アバターは動画の半分」であり、周囲のモーショングラフィックス制作がユーザーの詰まりポイントだった。その制作層をOSSで配布してコミュニティに磨かせ、自社有料プロダクト（Video Agent）では磨かれたHyperframes＋アバターの両輪を提供する青写真。「HTMLで動画を書く」標準を自社が握る狙いで、Linux/Kubernetes/Reactの「OSSで覇権を取る」パターンの動画版と位置づけられる。

@MakeAI_CEOによれば、ローンチPR動画そのものがClaude Code × Hyperframesだけで制作され、そのソースコードを「リツイートしてコメントしたら送る」形で配布する自己言及的なプロモーションが行われた。

## 費用対効果の主張と活用例（@MakeAI_CEO）

@MakeAI_CEOは、モーショングラフィックスの外注相場を「1本5〜10万円・納期2週間」と置き、クラウドワークス・ランサーズの個人なら3〜5万円、Lancersの上位層は10万円超と補足した上で、Hyperframes＋Claude Code環境では「外注コスト0円・納期10分」になると主張する。発信者向けの活用例として8つ（X記事冒頭のモーション量産・noteヘッダー動画・VSL冒頭15秒・PDF→45秒ピッチ・CSV→アニメチャート・LINEティザー・多言語展開・ショート動画量産）を挙げるが、いずれも同氏の提案であり効果測定の数字は示していない。「今が一番早い」「Rails／Next.js黎明期に触った人が今優位」という同氏の煽り文脈は、販促を兼ねた発信者の主張として割り引いて読むべきだと考えられる（wiki側の読み）。

## 観察ログ（未検証）

- 2026-04-16: HeyGenのVP of Product & Agent Engineering Bin Liu（旧Alisa共同創業者、2025年9月HeyGenに買収）がGitHubで公開。著者（@MakeAI_CEO）は「リリース24時間でX上が祭り状態」と記述（単一ソースの主張）。
- 2026-07-26: @MakeAI_CEOによれば、GitHubスター数は3万超まで伸びた（単一著者の観察）。同氏は採用チームのリストも公式リポジトリで公開されていると述べる。→ 2026-11-19にGitHub APIで86,511を確認（検証済み事実欄）。

## 検証済み事実

- ライセンスはApache 2.0（商用無制限）。
- レンダリングは決定論的（フレーム番号基準・ヘッドレスChrome `beginFrame`＋FFmpeg）で、同一入力から同一MP4を再現する設計。
- 2026-11-19: GitHub APIでheygen-com/hyperframesのスターは86,511・ライセンスApache-2.0・最終pushは2026-11-18。

## 問い

- 決定論レンダリングを使い、note/wiki記事公開をトリガーにヘッダー動画を自動生成するCI/CDを自分のvaultで組めるか（[[concepts/llm-wiki]]連携）。
- `data-*`属性ベースの中間記法は [[concepts/intermediate-notation-pattern]] のserialize/parse対と比べてどこまで双方向編集できるのか。
- Claude Opus 4.7/4.8世代で実際にどの程度安定して「動画として成立する」HTMLを出せるか、blank/play-modeで1本試す。
- FreeプランのVoice Turbo月3分で解説動画1本のナレーションは収まるか。超えた分を `npx hyperframes tts`（Kokoro）で賄ったとき声質の差はどれだけ出るか。
- 旧記述の `/faceless-explainer` など5レシピは現在の9スキルのどこへ行ったか（`/style-sampling` の5スタイルが後継か）。

## 関連

- [[tools/claude-code]]（動画生成のエージェント実行環境・`/hyperframes`スキル）
- [[models/claude-opus-4-7]]（Hyperframesが想定する実用モデル。転換点はOpus 4.5世代）
- [[concepts/intermediate-notation-pattern]]（AIにDSL/中間記法を操作させる設計思想の同型例）
- [[tools/remotion]]（同じくコードから決定論的に動画生成するFW）
- [[business/youtube-shorts-jidouka-marketing]]（動画量産マーケのレンダリング層として利用）
- [[tools/openmontage]] — エージェント型動画制作システム。Hyperframesをモーショングラフィックス向けレンダラとして採用
- [[companies/anthropic]]
- [[concepts/html-output-format]] — Anthropic公式のHTML出力実例集。HyperframesはHTMLを動画レンダリングの入力に使う応用形
- [[tools/diagram-design]] — HTML+SVGを図版の最終出力に使うリポジトリ。HTMLを成果物フォーマットに据える同系（出力は動画でなく静止図）
- [[concepts/opus-motion-design-studio]] — Opus 5.5 は Hyperframes が使える環境でも依存ゼロの seek(t)＋Playwright＋ffmpeg を既定で選ぶという観察。フレームワークを使わせるなら明示が要る
- [[tools/threejs-assets]] — three.js 向けの無料・商用可・帰属不要の低ポリ GLB モデル群。「Three.js 等の Web 資産がそのまま使える」本フレームワークで、モデルを自作せずシーンに置ける素材の供給源
- [[tools/creative-pipeline-repos]] — 磨き→動き→3D→画像→字幕→書き出しの 10 リポ一覧の 9 本目「HeyGen の無料ツール、Web ページを動画にする」（@flxrnc）
- [[tools/gemini-api-cli]] — Google 公式の CLI でコーディングエージェントにシェルから image／tts／music／video の生成を渡す。`/media-use` が HeyGen のカタログと生成モデルで担う「エージェントに素材と耳を足す」役割の Google 側の対応物
