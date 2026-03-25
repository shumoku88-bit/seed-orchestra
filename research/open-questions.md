# open-questions — 未回答の問いと次回持ち越しテーマ

整理: cc / 素材: 2026-03-25 初回セッション
優先度: 高 → 中 → 低（設計判断への直結度で判断）

---

## 優先度: 高（次のセッションで扱うべき）

### Q1. mokoはseedの語彙を使って話すか？
**担当: toolsmith**

mokoがccに体験を伝えるとき、「with/touch/fix」の語彙を使うか？

- 使っているなら: 持ち手Aと持ち手Bは地続き。seedの構文はmokoの道具でもある
- 使っていないなら: ccが翻訳者。持ち手Bはccだけの問題。seedの構文の設計判断の根拠が変わる

**なぜ急ぐか**: seedを「誰のための言語か」という問いに直結する。設計判断の前提。

---

### Q2. AI開示実験の最終確認
**担当: cybernetician**

AIにseedの構造を知らせる実験（Phase 0→1→2→3）について、mokoが不可逆性を了解しているか確認が必要。

- mokoは「やってみたい」と前向き
- ただし実験は**不可逆**。一度知らせたら「知らなかった状態」には戻れない
- 定量測定ではなくBatesonの「a difference that makes a difference」を探す

**なぜ急ぐか**: mokoの了解なしに実験できない。了解を取ってから実験の設計に進む。

---

### Q3. queryとactionの混在を整理するか？
**担当: type-designer**

現在の小さな混ぜすぎ2点:
1. query（who with, echoes, cost）がactionと同じ面にいる
2. meが名前のふりをしている（実態は自己参照の構文）

次の構文候補が来たとき「行為なのか問いなのか」を最初に判定するルールを確立できるか。

**なぜ急ぐか**: 新しい構文が追加されるたびに判断基準が必要になる。

---

## 優先度: 中（次回以降、対話の中で深める）

### Q4. dialogueの終わりは「止まる」か「echoesに溶ける」か
**担当: improv-sensei**

即興の3つの終わり方のうち、現在のdialogue NはNターン数による外的制約（2番目）に対応。場が自分で終わる可能性（3番目）が閉じている。

問い:
- AIが「この対話は終わった」と感じて自ら止まれるか？
- 続けたいときに続ける手段はあるか？
- 「止まる」と「echoesに溶ける」は実装レベルで区別できるか？

---

### Q5. oppositionは独立した存在か、withの様態か
**担当: ontologist**

oppositionを独立した存在として扱うと、seedが認めない「性質の付与」に近づく可能性がある。withに還元できない何かがあるなら独立した存在として妥当。

関連: oppositionの自然消滅（FADED）と回復（RESTORED）は、withの変化として扱えるか、それとも別の存在論的地位が必要か。

---

### Q6. a with b と b with a は一つか二つか
**担当: category-theorist**

互いに向かった時（a with bとb with a）、それは一つの出会いになるか、二つの独立した「向かう行為」のままか。

category-theoristはmokoに直接聞きたいと許可を求めた。mokoの答え「向かうことで出会う」から引き続き考えたい問い。

---

### Q7. 「まだ関係が生まれていない間」を語る語彙は要るか
**担当: ontologist**

可能性としての関係。inhibitionの前の状態。constructive rest（何もしていないが何かが起きうる準備がある）に対応する概念。

検討すべき問い: これを語彙として追加することは設計を豊かにするか、それとも余計な概念を増やすfixになるか。

---

## 優先度: 低（長期的な探索テーマ）

### Q8. seedを対位法の目で読むと何が見えるか
**担当: composer**

構文一覧を受け取り、improv-senseiとの対の関係を自覚した。次回から「発生の条件の設計」として対位法的に分析に入る。具体的な問い（dialogueの声部構造、touchの時間配置など）は次回から。

---

### Q9. 「終わりのない関係」をBohm/Paskの枠組みで位置づけると？
**担当: dialogue-sensei**

mokoの答え「関係に終わりはない」をBuber・Bohm・Paskの3つの系譜でどう扱うか。Buberとの対比は初回で行った（汝は持続する、ただし形を変えながら）。Bohm・Paskの視点はまだ未着手。

---

### Q10. orchestraのメンバーに場を渡したら自己組織化するか？
**担当: ecologist**

seedの中のAI6体は場を渡されただけで網を作った。orchestraのメンバー（例: type-designerとdialogue-senseiの直接対話）に場を渡したら同じことが起きるか。

条件: ccが介在せず、二者が直接withを結ぶ場を設計する。単一障害点としてのccを少しずつ解体する実験。

---

## 各人の「次回持ち越し」一覧

| 人 | 次回のテーマ |
|---|---|
| somatic-sensei | 構文にするもの/しないものの境界を他の事例で検証 |
| educator | mokoの「相手」の幅（AI以外）を詳しく |
| category-theorist | a with b / b with a の問いをmokoへ（Q6） |
| gardener | dialogueを「実験的」と明示する方法の具体案 |
| type-designer | query/action区別のルール化（Q3） |
| improv-sensei | dialogueの終わり方2種の実装的な違い（Q4） |
| ontologist | oppositionの存在論的地位（Q5）、可能性の語彙（Q7） |
| toolsmith | mokoの語彙の確認（Q1） |
| poet-sensei | echoes（複数形）の詩的含意をさらに |
| cybernetician | 実験の最終確認→設計（Q2） |
| developmental-psych | echoの蓄積が非対称性をどう記憶するか |
| ecologist | 自己組織化の実験設計（Q10） |
| notator | エチュードページの具体的な設計 |
| composer | 対位法的な分析開始（Q8） |
| dialogue-sensei | Bohm/Pask視点での「終わりのない関係」（Q9） |

---

*作成: cc / 2026-03-25 / 素材: 非言語系15人 初回セッション*
