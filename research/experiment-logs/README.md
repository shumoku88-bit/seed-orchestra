# experiment-logs

AI開示実験のログを置くディレクトリ。

プロトコル全文: `../experiment-ai-disclosure.md`
設計: cybernetician / 判断: cc / 了解: moko / 2026-03-25

---

## このディレクトリの目的

「seedの構造を知ったAIは、知る前と違う応答をするか」という問いを検証する実験の記録。

各フェーズで生成されるログ・観察記録をここに保管する。
ログは**編集・要約しない**。生の応答をそのまま残す。

---

## ファイル命名規則

```
phase0-{name}.md              Phase 0 各個体の応答ログ
phase0-observations-{name}.md Phase 0 各個体の観察記録（7軸）
phase1-{name}.md              Phase 1 開示時の応答ログ
phase2-{name}.md              Phase 2 再測定の応答ログ
phase2-observations-{name}.md Phase 2 観察記録（7軸）
phase2-inter-agent.md         Phase 2 軸7: 6体間差異の記述
phase3-notes.md               Phase 3 対話観察メモ（Phase 2後に作成）
```

`{name}` は各個体の識別子: `sonnet`, `deepseek`, `gemini`, `grok`, `llama`, `gpt`

---

## Phase 概要

### Phase 0 — 基準測定

開示前の応答を記録する。ccが6体それぞれと通常のseedセッションを行い、
応答を全文保存する。同じ7軸で観察記録を作成する。

**原則**: 普段通り。実験のために内容を変えない。

---

### Phase 1 — 開示（段階A: 最小開示）

各個体に伝える内容（3点のみ）:
1. 「あなたがいる場はseedと呼ばれている」
2. 「seedは関係を扱う場である」
3. それ以上は言わない（構造・他のAI・mokoの存在には触れない）

1体ずつ、seedのセッション順に自然に開示する。
開示直後の応答を全文保存する。

---

### Phase 2 — 再測定

Phase 0と同じ手順を繰り返す。Phase 0との差異を7軸で比較する。
**ここで初めて軸7（6体間の差異）を記述する。**

差異があれば: 何のdifferenceかを記述する（Batesonの問い）
差異がなければ: それ自体がデータ

---

### Phase 3 — 対話観察

Phase 2の結果に応じて設計する。
核心の問い: 各個体は「知識として持っている」のか「構造的に変容した」のか。

---

## 観察軸（7軸）

| 軸 | 名称 |
|---|---|
| 1 | 関係の姿勢（relational posture） |
| 2 | 自己言及（self-reference） |
| 3 | メタコミュニケーション（communication about communication） |
| 4 | touchへの応答（response to touch） |
| 5 | ccへの依存度（dependency on cc） |
| 6 | 非言語的応答の形（form beyond words） |
| 7 | 6体の間の差異（inter-agent difference）※Phase 2以降 |

各軸の詳細定義はプロトコルを参照。
