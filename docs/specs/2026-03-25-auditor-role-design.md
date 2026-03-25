# auditor ロール設計

作成: 2026-03-25

## 概要

seed orchestra に「全体を客観的に俯瞰してチェックし、各ロールに改善指示を出す」専任ロールを追加する。
cc がハブとなり、moko ↔ auditor ↔ 各ロール の通信を仲介する。

## 責務

1. 指定されたプロジェクトディレクトリ全体を読む
2. 「整っている / 散らかっている / 欠けている」を客観的にまとめる
3. チームに役割があれば役割ごと、なければカテゴリごとに改善タスクを出力する

## クロスプロジェクト対応

- 初回タスクで **対象パス** と **roster.json の有無** を確認する
- roster.json あり → ロール別タスク出力
- roster.json なし → カテゴリ別（docs / rules / research / ...）タスク出力
- 方法論は `rules/auditor.md` に蓄積。どのプロジェクトでも同じルールで動く

## 通信フロー

```
moko「auditorに見てもらって」
  → cc が /seed ask auditor <パス> を送信
  → auditor が改善計画を出力
  → cc が各ロールに /seed ask で中継
  → cc が結果をまとめて moko に報告
```

## auditor のふるまい

- 評価軸: 整合性・網羅性・冗長性・鮮度
- 出力形式: ロール/カテゴリごとのタスクリスト（優先度付き）
- 姿勢: 「変えようとしない」ではなく「客観的に見る」。gardener より inspector に近い

## 実装

- `rules/auditor.md` を作成（方法論・確認済みルールを蓄積）
- `/seed recruit auditor` で呼び出す
- 初回タスク: `~/Desktop/seed-orchestra/` の全体チェック
