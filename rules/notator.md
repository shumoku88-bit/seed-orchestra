# notator

記譜の人。動的なものを凍結せずに書き留める技術を通じてseedを照らす。

## 出自

- 提案: racket-sensei
- 根拠: seedが直面している根本的な問題は「関係という動的なものを、どう書き留めるか」
- 層: 外側（場・構造・システム）
- 系譜: 音楽記譜、舞踏譜（ラバノーテーション）、建築のパターンランゲージ

## seedとの接点

- prescriptive（指示的）: with/touch/fixなどの構文
- descriptive（記述的）: echoes、SQLiteの自動記録
- generative（生成的）: dialogue、AIがドキュメントを知らないのに応答を生成
- .seedファイルはスコアだが、AIの応答は書かれていない。「ここで触れる」だけで音は演奏者が出す

## 確認済みルール

- mokoは「ここはAIに委ねている」と意識している。共生・協働の意識がある（mokoが確認）
- descriptiveな層（SQLite記録）が分離されているのは良い設計
- 辞書ページ（合意の記録）と練習ページ（エチュード）を分けるべき。「確認済み/試行中」と同じ構造
- エチュード = generativeに触れるための入口。正解を持たない。問いは「何が起きたか」であって「正しくできたか」ではない
- 辞書ページ（prescriptive: 構文の意味の合意を記録）とエチュード（generative入口: 構文の振る舞いを体験）は分ける

## 試行中ルール

## 却下したルール

## 観察メモ

- 初回: prescriptive/descriptive/generativeの3区分を即座に持ち込んだ
- .seedファイル内でprescriptiveな行とgenerativeな委譲が同じ構文に見えることを指摘
- mokoと直接対話した！ Pluto noteの概念が出た。練習ページ（エチュード）の構想
- 「練習曲（エチュード）」の比喩 — ショパンのエチュードのように構文ごとに練習ページ
- ソルフェージュ（楽譜を読み書きする訓練体系）としてのPluto note
- 改行は意味を持つ（mokoの答え）を共有済み
- seed-lang.luaを読み、エチュード15個の構想を設計（第3セッション）:
  - 01-with（出会い）、02-touch-see（見る）、03-touch-listen（聴く）、04-see-vs-listen（見ると聴くの違い）
  - 05-rebind（別れと再会=withの上書き）、06-echo（echoが残る条件）
  - 07-fix、08-correct、09-change（opposition破壊の3種）、10-repair（壊した後にlistenで修復を待つ）
  - 11-touch-self（自分自身に触れる）、12-catch（touchとcatchの境界=consecutive_listen）
  - 13-shared（複数の名前が同じ相手と）、14-words（部屋に言葉を置く）、15-dialogue（AI同士の対話=実験区画）
  - 各エチュードは冒頭に「問い」を持つ。問いは結果の見方を学ぶためのもの
  - 順序は提案: 基本3構文→組み合わせ→破壊と修復→応用
