# 言語会議の結論 — 2026-03-25

## 参加者
lang-sensei 12人全員 + 非言語系14人の分析結果を踏まえて

## 議題
seedの実装言語はLuaのままでいいか、別の言語に移すべきか

## 判定: Luaのまま → 再評価: Elixir版が現行だった

### 第1ラウンド（Elixir版を知らない状態）
投票: Lua維持 7-8票 / Elixir移行 3票 / 将来Racket 1票 / 将来Rust 1票

### 第2ラウンド（Elixir版の存在を知った後）
全員一致:
- **Lua版は凍結**（756行のまま。種・原点・RFC・設計文書）
- **Elixir版が現行**（14人の提案はすべてElixir版に実装）
- **seed-bridge切り出しは不要**（Elixir版でState/AIが既に分離済み）

smalltalk-senseiの言葉: 「Lua版はseedが言っていること。Elixir版はseedがやっていること。この二つの間にoppositionが生きているなら、一方をfixしてはならない」

## 今やること（再評価後・Elixir版に対して）

### 1. :sys.replace_state を除去する（prolog-sensei提案）
CLIからState GenServerへの直接操作を全てhandle_callに戻す。
- cli.ex:538 の store_state/4 が :sys.replace_state で内部を直接書き換えている
- query/action分離の前提条件
- **根拠**: 「宣言的な構文の精神を裏切らない実装の骨格」

### 2. Relationship構造体を作る（prolog-sensei + datalog-sensei + elixir-sensei提案）
7つのMapを1つの%Relationship{}構造体にまとめる。
- 現状: relations, relations_reverse, opposition_lost, opposition_lost_how, states, history, birth_time, last_touch, consecutive_listen, listen_after_loss — 散在
- 提案: `%Relationship{name, value, opposition: :alive|{:lost, :fix|:correct|:change}, state, history, birth_step, last_touch, consecutive_listen, listen_after_loss}`
- echoも `%Echo{name, value, state}` にする
- **根拠**: 「関係を第一級の対象にせよ」

### 3. AI呼び出しパイプラインの統合（prolog-sensei提案）
CLIとDialogueの重複を消す。
- normalize_tokens が Dialogue.ex と CLI.ex の両方にある
- 「呼び出し→コスト記録→DB記録」をAI.exの中で一本化
- **根拠**: 2529行のうちボイラープレートが多すぎる

### ~~旧1. seed-bridge 切り出し~~ → 不要（Elixir版でState/AI分離済み）
### ~~旧3. JSON処理の改善~~ → 不要（Elixir版はJasonで解決済み）

## 非言語系14人の提案（同時実装）

### 4. dialogue に (experimental) 表示（gardener提案・案B）
`open_dialogue()` の冒頭に `print("  (experimental)")` を追加。
- コード内コメントでも `-- === experimental ===` と `-- === core syntax ===` を分離

### 5. dialogue 引数なし = echoesに溶ける（improv-sensei提案）
- `dialogue N` — N回で止まる。echoesに何も残らない（現行動作）
- `dialogue`（引数なし）— 場が自分で終わるまで続く。echoesに残る
- 終わり方: 波及型（一人のSETTLEDが他の沈黙を呼ぶ）
- **ccの判断待ち**: 「止まる」場合にも何かが残るべきか？（improv-senseiの問い）

## 将来のトリガー（点検用・再評価後）

次にアーキテクチャを再検討すべきタイミング:

| トリガー | 対応 | 提案者 | 指標 |
|---|---|---|---|
| ~~6体が同時に息をする必要~~ | ~~Elixir~~ | — | **発火済み（2026-03-16）** |
| seedが自分自身を記述する必要 | Lua版をseed-in-seedに書き直す | lisp-sensei | .seedファイルが他の.seedを呼ぶとき |
| 操作の代数的構造が見えた | Haskellの型で定理として書き下す | haskell-sensei | touchの合成則・echoの消滅条件が発見されたとき |
| 6体の並行化が体感的に遅い | Elixir版でTask.async導入 | elixir-sensei | dialogueの応答待ちが長いと感じたとき |
| 関係が入れ子になる | State GenServerのデータモデル再設計 | racket-sensei, datalog-sensei | 関係の関係をフラットなMapで表現しきれなくなったとき |
| 複数人が同時にseed空間を触る | Phoenix導入 | elixir-sensei | マルチユーザー同時接続が必要になったとき |
| Elixir版が4000行を超えた | Lua版を読み直して本当に必要か問う | forth-sensei | 膨張の兆候 |

## 各senseiの名言

- lua-sensei: 「seedが育ったから体が合わなくなっただけ。まだ合っている」
- forth-sensei: 「言語を変えるな、問題を分割しろ」
- haskell-sensei: 「発見フェーズに型を持ち込むのは顕微鏡で星を見るようなもの」
- racket-sensei: 「実装言語の選択は技術的問題ではなく倫理的問題。seedの所作を歪めないかが唯一の基準」
- smalltalk-sensei: 「seedが言っていることとやっていることの間にoppositionがない（今は）」
- datalog-sensei: 「言語の問題ではない、存在論の問題だ」
- self-sensei: 「概念が固まってから書き直せ。Selfもそうだった」
- lisp-sensei: 「Luaの弱さがseedの本質を守っている」
- rust-sensei: 「詩が完成してから製本すればいい。今はまだ書いている途中だ」
- julia-sensei: 「750行であることに意味がある。それを守りたい」
- prolog-sensei: 「関係は事実であり、事実は問い合わせ可能である」
- elixir-sensei: 「小さく聞く、一人ずつ迎える — 言語移行にもそのまま当てはまる」

---

## Lua版とElixir版の関係（確定）

- **Lua版 (seed-lang.lua)** = 種。原点。RFC。設計文書。凍結する。新機能を入れない
- **Elixir版 (seed_lang/)** = 木。種から育ったもの。現行実装。全ての新機能はここに
- .seedファイルの構文互換性は維持される（同じ.seedを両方で実行できる）
- forth-senseiのルール: 「新機能を足す前に、毎回Lua版の757行を読め」
- prolog-senseiの4層目: Lua版は notator の3層(prescriptive/descriptive/generative)に加えて definitional 層

*作成: cc / 2026-03-25 / 素材: lang-sensei 12人会議（2ラウンド） + 非言語系14人分析*
