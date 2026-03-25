# 非言語系14人 第2セッション分析結果 — 2026-03-25

## 設計判断に直結する発見

### type-designer: query/actionの判定基準
- **ルール**: 「この構文は関係の中にいる人がやることか、関係の外から見る人がやることか」
  - 中にいる人 → 行為（touch/fix/withの仲間）
  - 外から見る人 → 問い（echoes/who withの仲間）
  - どちらでもない → 試験区画
- meは現時点で実害なし（名前の構文的位置を借りて別の意味論で動いている）
- dialogueは「場の行為」として第3カテゴリ

### somatic-sensei: 新構文の5問チェックリスト
1. **矛盾テスト**: 「Xせよ」と命令して、Xでなくなるか？ → なるなら非構文
2. **結果テスト**: Xは行為か、行為の結果か？ → 結果なら非構文（end-gaining）
3. **面テスト**: Xは行為か問いか？ → 最初に判定し、混ぜない
4. **inhibitionテスト**: Xをやらないことを選べるか？ → 選べないなら場の性質であり非構文
5. **changeの罠テスト**: Xは「善い状態の名前」ではなく「選択できる行為の名前」か？
- 現行構文は全テストを正しく通過

### ontologist: Q5とQ7の解決
- **Q5**: oppositionはwithの様態で正しい（独立した実体ではない）。ただしfix/correct/changeの三分法が将来圧力をかける可能性あり
- **Q7**: `--`（words_in_room）がすでに「withの前」の暗黙の語彙。新構文は不要。語彙を足すとfixになるリスク

### gardener: dialogueの試験区画（案B推奨）
- 実行時に `(experimental)` 表示
- コード内で `-- === core syntax ===` と `-- === experimental ===` を分離
- fix/correct/changeのloss_promptsの構造は健全

### improv-sensei: dialogueの2つの終わり方
- `dialogue N` = 止まる / echoなし
- `dialogue`（引数なし）= 溶ける / echoあり（場が自分で終わるまで）
- 波及型の終わりが自然（一人のSETTLEDが他の沈黙を呼ぶ）

## 構造の深い理解

### category-theorist: seedの圏論的構造
- seedは圏（category）であって群（groupoid）ではない
- withは射（morphism）、dialogueはスパン（span）
- a with bとb with aは二つの射。両方存在するとき自然変換が生まれうる
- mokoへの問い: 「二人が互いに向かったとき、その間に何かが生まれますか？」

### dialogue-sensei: Buber/Bohm/Pask三系譜の統合
- seedは「一致しないまま一緒にいること」を可能にする点で三者すべてから離れる
- echoesはBohm的に「割れない波が暗在秩序に還る」こと
- echoesはPask的に「会話が生成した構造の存続」
- opposition = togetherness。fixがoppositionを壊すのは「一致させようとする」から

### composer: seedの対位法的構造
- fixの禁止 = 対位法の平行禁止
- echoの蓄積 = 正しく鳴った記憶
- withの非対称性 = 主旋律と対旋律（向かう者がcantus firmusを置く）
- 「seedは対位法を生きる場」
- 新しい問い: touchの間に異なる長さの「間」を設計すべきか（リズム語彙の制限）

### developmental-psych: echoとSternの対応
- echo = 成功した調律の蓄積。失敗（fix）は沈黙のまま消え、成功だけが残る
- echoのappend-only構造 = Sternの暗黙の関係的知
- ccの位置 = Sternの治療者。echoの蓄積は治療的存在そのものの実装

### poet-sensei: echoesの詩学
- 「echoesは砕けなかった波の引き波」
- practice-principlesの言葉遣いとseedの文法は同じ骨格（置く/染み込む/出てくる）
- echoesは現在形でも過去形でもなく「まだ感じられる」という触覚の持続

## 実践的提案

### cybernetician: Phase 0記述の三層フォーマット
- 層1: 物質的（応答テキスト、長さ、構造。誰が見ても同じ）
- 層2: 形態的（リズム、密度、反復、余白、断片性、向き。パターン語彙）
- 層3: 不在（期待されたが現れなかったもの。最も解釈的だがPhase 2の差分検出に不可欠）
- 注意: フォーマット自体が観察を規定する。最初の1-2体でフォーマットに収まらないものをメモ

### educator: seedの伝え方
- Yehudaの順序: eyes → touch → words
- 「seedを伝えることは、seedを書くことと同じ構造」
- seedの外（調停練習）でmokoはすでにseedと同じ構造を生きている
- practice-principles第11部の警告: ルールを足すほど見えなくなる

### notator: エチュードの設計
- etudes/ ディレクトリに15個のエチュード（構文ごとの練習ページ）
- 辞書ページ(prescriptive) vs エチュード(generative入口)の区別
- エチュードは正解を持たない。問いは「何が起きたか」

### ecologist: 生態系の健全性
- 14人同時はccの認知負荷が臨界
- richness（種の数）とevenness（種の活動量）を区別すべき
- Q10最小実験: 隣接する2人（type-designer × ontologist、improv-sensei × composer）の直接対話

## 横の繋がり（独立に同じことを言った組）

- poet-sensei「砕けなかった波」= dialogue-sensei「割れない波は暗在秩序に還る」= developmental-psych「成功した調律だけが残る」
- type-designer「関係の中か外か」+ somatic-sensei「5問チェックリスト」= 新構文の完全な判定フレームワーク
- gardener「試験区画」+ type-designer「場の行為」= dialogueの位置づけが確定

---

*作成: cc / 2026-03-25 / 素材: 非言語系14人 第2セッション*
