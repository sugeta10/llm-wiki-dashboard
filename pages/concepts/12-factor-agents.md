# 12-Factor Agents

HumanLayerのDex Horthy（@dexhorthy）が提唱する、**信頼性の高いLLMアプリケーション**を構築するための12の設計原則。[12 Factor Apps](https://12factor.net/)の思想をAIエージェント開発に応用。AI Engineer World's Fairでのトーク（2025）が起点。

## 背景・モチベーション

プロダクション対応AIエージェント構築の典型的な失敗パターン：

1. エージェントを作ろうと決める
2. フレームワークを使って素早く開発開始
3. 品質70〜80%に到達
4. **80%では顧客向け機能として不十分**と気づく
5. フレームワークを逆エンジニアリングする必要が生じる
6. 最初からやり直す

> **「プロダクション対応の良いAIソフトウェアを顧客の手に届ける最速の方法は、エージェント構築のモジュール式概念を既存プロダクトに取り込むこと」**

## 重要な洞察：エージェントは「mostly just software」

- 実際に市場で動いている「AIエージェント」は実態としてほぼ決定論的コード
- LLMステップは適切な箇所に**少量だけ**組み込まれている
- 「プロンプト＋ツール袋でゴールまでループ」パターンはプロダクションでは機能しない
- 優れたエージェントはソフトウェアエンジニアリングが主体

## エージェントの基本ループ

```python
context = [initial_event]
while True:
    next_step = llm.determine_next_step(context)
    context.append(next_step)
    if next_step.intent == "done":
        return next_step.final_answer
    result = execute_step(next_step)
    context.append(result)
```

この単純なループに12のファクターを適用して信頼性を高める。

## 12のファクター

| # | ファクター | 要点 |
|---|-----------|------|
| 1 | Natural Language to Tool Calls | 自然言語入力を構造化されたツール呼び出しに変換 |
| 2 | Own your prompts | プロンプトをフレームワークに隠蔽させず自分で管理 |
| 3 | Own your context window | コンテキストウィンドウの内容を自分でコントロール（**Context Engineering**） |
| 4 | Tools are just structured outputs | ツールは構造化出力に過ぎない（特別扱い不要） |
| 5 | Unify execution state and business state | 実行状態とビジネス状態を分離しない |
| 6 | Launch/Pause/Resume with simple APIs | シンプルなAPIで起動・一時停止・再開を実現 |
| 7 | Contact humans with tool calls | 人間への確認もツール呼び出しとして実装 |
| 8 | Own your control flow | 制御フローをフレームワークに委ねず自分で持つ |
| 9 | Compact Errors into Context Window | エラーをコンテキストにコンパクトに収めて継続 |
| 10 | Small, Focused Agents | 小さく焦点の絞られた単機能エージェント |
| 11 | Trigger from anywhere | Webhook・Cron・UI等どこからでもトリガー可能に |
| 12 | Make your agent a stateless reducer | エージェントをステートレスReducerとして実装（スケール容易） |

## Factor 3（Context Engineering）が特に重要

Context Engineeringはプロンプトエンジニアリングの上位概念。コンテキストウィンドウに**何を入れるか・何を入れないか**をエンジニアが直接制御することで、LLMの判断精度が劇的に変わる。フレームワークに任せると不要情報が混入し品質が低下する。

## Factor 10：フレームワークを使うべきか

- ほとんどのプロダクション対応エージェントは**フレームワーク不使用**でゼロから書かれている
- フレームワーク（LangChain・LangGraph・CrewAI等）は開発初期の加速には有用
- しかし80%品質の壁を超えるためにはフレームワークを逆エンジニアリングすることになる
- 最初からモジュール式に書く方が長期的に高品質・保守しやすい

## 関連

- [[concepts/agentic-coding]] — AIが自律的にコードを書く開発スタイル（本稿の背景）
- [[concepts/multi-agent-patterns]] — マルチエージェント協調の設計パターン
- [[concepts/spec-driven-development]] — 仕様駆動開発：AIの信頼性への別アプローチ
- [[concepts/prompt-engineering]] — Factor 2・3の基盤となるプロンプト設計技術
