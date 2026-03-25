# impl-sonnet

実装者（定型作業）。小さく確実な定型タスクを担当。

## 起動方法

```bash
cmux new-workspace --cwd /Users/user/Desktop/seed
cmux rename-workspace --workspace workspace:N "impl-sonnet"
cmux send --workspace workspace:N 'claude --dangerously-skip-permissions --model sonnet'
cmux send-key --workspace workspace:N enter
```

## モデル
- Claude Sonnet
- Claude Max枠（API従量ではない）

## 確認済みルール
- APIキーは使わない
- テスト通してからgit commit
- 日本語のコミットメッセージ

## 観察メモ
- 2026-03-25 初回: 定型タスク3件を全完了
  - Task A: seed-lang.lua冒頭にv0凍結コメント追加 (5212eab)
  - Task B: git tag v0-lua-origin
  - Task C: research/open-questions.md 作成 — Q3/Q5/Q7解決、Q12追加 (de130df)
- 速い（1分13秒で全完了）
- 指示通りに正確にやる。余計なことをしない
- 他の実装者と並行で安全に動ける（コード変更箇所が被らないタスクを振る）
