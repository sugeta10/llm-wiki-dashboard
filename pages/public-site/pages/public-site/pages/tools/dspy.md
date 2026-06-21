# DSPy

**DSPy**は、手動のプロンプトエンジニアリングを排除し、プロンプト最適化を機械学習問題として扱うPythonフレームワーク。スタンフォード大学NLPグループが開発。

## 核心思想

| 領域 | 人手での作業 | DSPyが置き換えるもの |
|------|------------|-----------------|
| ディープラーニング | 特徴量エンジニアリング | 中間層として学習 |
| DSPy | プロンプトエンジニアリング | 調整可能プロンプトとして学習 |

プロンプトを頑張って作る作業を**最適化問題**に落とし込む。目的関数は微分不可能なブラックボックスでも構わない（勾配フリー最適化を使う）。

## 主要概念

### Signature

入力・出力の型シグネチャをクラスで定義する。明示的なプロンプト文を書く必要がない。

```python
class NarutoSignature(dspy.Signature):
    polite_sentence = dspy.InputField(desc="です・ます調の一文")
    rationale = dspy.OutputField(desc="変換の推論過程")
    transformed = dspy.OutputField(desc="ナルト口調に変換した文")
```

### Module

SignatureをラップしてエージェントのForwardパスを定義する。`dspy.ChainOfThought`等のビルトインModuleが使える。

### Optimizer（最適化器）

訓練データ（`dspy.Example`リスト）と評価指標（スカラー値を返す関数）を与えて、Signatureに最適なプロンプトを自動生成する。

## 主要Optimizer

### COPRO

勾配フリーで候補プロンプトを反復改善する基本的なOptimizer。

```python
from dspy.teleprompt import COPRO
prompt_optimizer = COPRO(metric=metric, verbose=True)
prompt_tuned = prompt_optimizer.compile(model, trainset=trainset, eval_kwargs=kwargs)
```

### GEPA（進化計算系）

評価スコアに加えて**フィードバックテキスト**を利用する進化計算系Optimizer。リフレクション用LLMが改善提案を生成し、それをプロンプト改善に活用する。

```python
reflection_lm = dspy.LM('openai/gpt-4o-mini', temperature=1.0, max_tokens=2048)
gepa = dspy.GEPA(
    metric=metric_with_feedback,
    auto='light',
    reflection_lm=reflection_lm
)
gepa_compiled = gepa.compile(model, trainset=trainset, valset=valset)
```

COPROよりはるかに詳細なプロンプトを生成する傾向がある。

## 評価指標の設計

外部LLMに評価させるパターンが「モダン」とされる（安価なLLMを訓練するために高価なLLMで評価）。

```python
class Assess(dspy.Signature):
    """評価観点をチェックし改善ヒントを返す"""
    assessment_transformed = dspy.InputField(desc="生成されたテキスト")
    assessment_question = dspy.InputField(desc="評価観点の質問")
    assessment_answer = dspy.OutputField(desc="yes / no 判定")
    assessment_feedback = dspy.OutputField(desc="改善ヒント")
```

## PyTorchとの比較

PyTorchよりもKerasに近い使い心地（高度に抽象化された訓練ループ）。「Define by Run」ではなく宣言的なDefine and Run的な設計に近い。データ・モデル・オプティマイザーの責務分離という思想はPyTorchから影響を受けている。

## 観察ログ（未検証）

- 2026-05-31: @MakeAI_CEO が「海外のAI研究者界隈で『日本人のプロンプトはDSPyすら知らない化石レベル』と煽る人物がいる」と紹介（出典記事は未取得）。手書きプロンプト文化に対し、DSPyのような**プロンプト最適化を機械化する設計**が先端標準として位置づけられている、という言説の温度感を示す事例。煽り文脈であり技術的主張の中身は未検証

## 問い

- 手書きプロンプト（[[concepts/prompt-engineering]]）からDSPyへ移る損益分岐点はどこか——訓練データ・評価指標を用意するコストが、手チューニングを上回る出力量・タスク数は
- DSPyのOptimizer（COPRO/GEPA）と[[concepts/eval-loop]]の関係：eval loopが「人が回す品質ゲート」なら、DSPyは「その採点を目的関数にしてプロンプトを自動探索する」自動化版と捉えてよいか

## 関連

- [[concepts/prompt-engineering]] — DSPyが自動化しようとしているプロンプト設計技術
- [[concepts/spec-driven-development]] — プロンプト自動化と仕様駆動の関係
- [[concepts/eval-loop]] — 評価指標で出力を採点するループ。DSPyはそのスコアを最適化の目的関数に使う
- [[tools/hermes-agent]] — GEPAオフライン最適化を実装するエージェント基盤
- [[tools/hermes-agent-self-evolution]] — DSPy+GEPAでスキル/プロンプト/コードを自動進化させる専用リポ（GEPAはICLR 2026 Oral）
