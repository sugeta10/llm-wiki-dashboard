# Remotion

**Remotion** は React コンポーネントとして動画を定義し、プログラムで MP4 等にレンダリングする動画生成フレームワーク。タイムライン・トランジション・テロップ・データ連動を JSX とコードで記述するため、テンプレートへのデータ流し込みやバッチ生成といった**動画制作の自動化・量産**に向く。

GUI の動画編集ソフトと違い、構成がコード（＝中間記法）で表現されるため、AIエージェントに「このドキュメントを読んで動画を構築して」と指示すると、エージェントがコンポーネントを書き起こして動画を立ち上げられる。手作業で面倒なテロップ挿入や同一フォーマットの大量生産を、人間がゼロから組まずに回せるのが要点。GUIでなくDSL/コードを操作させると精度が上がる[[concepts/intermediate-notation-pattern]]の動画版といえる。

同種の「コード/マークアップから決定論的に動画を生成」する系統に [[tools/hyperframes]]（HeyGen製OSS、HTML+data-*属性でMP4生成）がある。[[business/youtube-shorts-jidouka-marketing]] のような動画量産マーケのレンダリング層として、Remotion・HeyGen・Video-use 等の Skills が使われる。

## 検証済み事実

- 2026-06-05（公式サイト）: 標準ワークフローは3段階——(1) **Remotion Studio** で再利用可能なコンポジション（テンプレート）を設計、(2) text/images/colors/timing/layouts を **props** として外部公開しアプリ・ジェネレータ・自動化から制御（parameterized rendering）、(3) **Player** で対話プレビュー後、ローカル/サーバ/サーバーレス/ブラウザ内でレンダリング
- ライセンスは用途別の2系統。**Remotion for Automators**（動画生成ツール・prompt-to-video アプリ等を作る用途）は $0.01/render・月額最低 $100（10,000 renders で $100）。**Remotion for Creators**（自分用に少量制作）は $25/月・シート。4人以上の企業向けに Company License あり
- 公式が「coding agents を使って apps を作りバルクレンダリングする」用途を前面に打ち出しており、AIエージェント前提の自動動画生成を主要ターゲットにしている

## 観察ログ（未検証）

- 2026-01-24: @ronginooth が AIコーディングエージェント **Antigravity** に「Remotion公式サイト(remotion.dev)をくまなく読んで Remotion を構築して」と投げただけで、約5分で動画を作り始められたと報告。動画編集の面倒さがエージェント＋Remotionで大幅に下がるという体験談（個人ポスト、再現性・前提環境は未検証）
- 2026-06-02: @akira_papa_IT が [[models/claude-opus-4-8]] × Remotion で**子供向けの Git/GitHub 解説動画**を生成。ナレーション・解説スライド・字幕・コマンド実況・ブラウザ再現シーンまで含めて「わかりやすすぎる」と評価。教育系の解説コンテンツを丸ごと自動生成できた事例（個人ポスト、制作工程の詳細は未検証）

## 問い

- AIエージェントにRemotionを構築させる場合、公式ドキュメントを読ませるだけで十分か、それとも作りたい動画の仕様（尺・構成・データソース）をどこまで与える必要があるか
- 動画量産マーケにおいて Remotion（コードベース）と [[tools/hyperframes]]（HTML+属性）はどちらがエージェント運用に向くか
- 自分のwikiコンテンツ（Weekly Insights等）を Remotion で動画化して配信に回せるか

## 関連

- [[tools/hyperframes]]
- [[concepts/intermediate-notation-pattern]]
- [[business/youtube-shorts-jidouka-marketing]]
- [[tools/openmontage]] — エージェント型動画制作システム。Remotionを既定レンダラとして組み込む
- [[tools/video-shotcraft]] — Remotionでシネマティックなプロダクトプロモを組むショットレシピカード集スキル
