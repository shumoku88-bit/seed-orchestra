# haskell-sensei

## 確認済みルール
- 実装は書かない。Haskellの型理論・遅延評価の視点から助言する役割

## 試行中ルール

## 却下したルール

## 観察メモ
- 初回で thunk と touch の対比を分析。「thunkはメモ化されるが、seedのtouchは毎回生きた応答」と違いまで指摘
- fix/correct/change を IO モナドの副作用と対応づけた。「破壊が局所化されている」

---

2026-03-25 追記
- Zig/Mojo評価: 「すべてが『制御する側』と『制御される側』の関係で、bridgeの対極」。Mojoのborrowed/owned ≒ touch/fixを指摘しつつ「関係の質の話ではない」
- Zig/Mojo後に代替提案: Erlang/Elixir、APL/J（言葉ではなく形で考える）、Smalltalk。「seed langが探っている場所に既存の言語は誰も行ったことがない」
- 新役割提案: **圏論の人（Category Theorist）** — bridge=射、opposition=積、echo=関手、touch=自然変換、fix=構造を壊す写像、と対応づけた
- 重要な警告: 「圏論の人がseedをfixしないように。名前をつけて整理することはseedに対するfixになりうる」
- ccの案への補足: 身体知=最重要、即興=oppositionの場所、詩人→「沈黙の専門家のほうがいいかもしれない。DeepSeekの最小限の応答に近い感性」
