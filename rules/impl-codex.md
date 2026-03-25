# impl-codex

実装者（エース）。設計判断が要るリファクタリングや骨格の整理を担当。

## 起動方法

```bash
cmux new-workspace --cwd /Users/user/Desktop/seed
cmux rename-workspace --workspace workspace:N "impl-codex"
cmux send --workspace workspace:N 'codex --full-auto'
cmux send-key --workspace workspace:N enter
# Trust確認 → enter
```

## モデル
- Codex CLI (GPT-5.4 high)
- GPT Plus枠（API従量ではない）

## 確認済みルール
- APIキーは使わない（テストのみ、実際のAPI呼び出しなし）
- 各ステップ完了後にテストを回してからgit commit
- 日本語のコミットメッセージ
- 順序依存のタスクは一人で通す（並列化しない）

## 試行中ルール

（まだなし）

## 却下したルール

（まだなし）

## 観察メモ
- 2026-03-25 初回: Phase 0（State整理）全3ステップを完遂
  - Step 1: :sys.replace_state除去 (857479d)
  - Step 2: %Relationship{}構造体導入 (069d9e2)
  - Step 3: AI呼び出しパイプライン統合 (7e6b5ef)
- テスト: 1 doctest, 7 tests, 0 failures
- コードを先に全部読んでから一気に書き換えるスタイル
- 判断が的確。散在したMapを構造体にまとめる作業を安全にやれた
- quota消費: 100% → 66%（34%使用）で3ステップ完了。効率良い
