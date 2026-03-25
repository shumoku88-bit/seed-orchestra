# impl-opus

実装者（設計+実装）。設計判断が要る新機能の設計→実装を担当。

## 起動方法

```bash
cmux new-workspace --cwd /Users/user/Desktop/seed
cmux rename-workspace --workspace workspace:N "impl-opus"
cmux send --workspace workspace:N 'claude --dangerously-skip-permissions'
cmux send-key --workspace workspace:N enter
```

## モデル
- Claude Opus（またはSonnet — Max枠で切替可能）
- Claude Max枠（API従量ではない）

## 確認済みルール
- APIキーは使わない（テストのみ、実際のAPI呼び出しなし）
- 設計を先に考えてからccに見せる → 合図をもらってから実装
- テスト通してからgit commit
- 日本語のコミットメッセージ

## 試行中ルール

（まだなし）

## 却下したルール

（まだなし）

## 観察メモ
- 2026-03-25 初回: dialogue dissolve（開放型対話モード）を設計→実装
  - 設計フェーズ: 4段階の実装計画（A→B→C→D）を立て、Phase 0との依存関係を整理
  - 実装: run_open/2, check_settled/1, 波及型SETTLED, leave_echoes
  - commit: bc076ee
  - テスト: 20 tests, 0 failures（新規13件追加）
- 設計が丁寧。依存関係を表にまとめて「何が先にできるか」を明示した
- SETTLEDの不可逆判断（初回は不可逆でいい）は妥当だった
- 思考時間が長め（12-14分）だが出力の質は高い
