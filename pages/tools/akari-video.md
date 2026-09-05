# Akari Video

[[tools/claude-code]]や[[tools/openai-codex|Codex]]のようなコーディングハーネスをフル活用して動画編集を最大8割自動化することを狙う、オープンソースのAIネイティブ動画編集ツール。開発者の@ryoma_nakajimaが2026-07-26にプレリリースを発表した。

[[tools/video-use]]（素材フォルダを渡してClaude Codeと会話しながら編集する既存OSS）と同じ「コーディングエージェントを動画編集に転用する」系譜に位置するが、Akari Videoは自動化率という具体的な数値目標（最大8割）を打ち出している点が特徴。

## 問い

- 「最大8割自動化」の内訳（カット・字幕・カラー調整のどこまでを指すか）はプレリリース時点では未公開。リポジトリ本体を確認する価値があるか
- [[tools/video-use]]・[[tools/openmontage]]と比べて何が差別化点か（対応フォーマット・UIの有無等）

## 関連

- [[tools/video-use]] — 先行する同系統OSS（素材フォルダ→final.mp4）
- [[tools/claude-code]] — フル活用するハーネスの一つ
- [[tools/video-shotcraft]] — Remotion系の新規制作特化スキル（Akari Videoは既存素材の編集寄り）
- [[tools/ffmpeg-skill]] — 独立ツールでなくSkillとしてFFmpegをエージェントに使わせる別アプローチ（@kebura_P）
