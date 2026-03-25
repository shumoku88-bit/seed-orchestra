# アセスメント: seed-orchestra（初回）

実施: 2026-03-25 by auditor

---

## 全体所見

**整っているもの:**
- rules/ の設計哲学が一貫している。各ロールの「出自」「seedとの接点」「確認済みルール」「観察メモ」という構造が守られている
- research/ の主要ドキュメント（discoveries.md, open-questions.md, experiment-ai-disclosure.md, language-decision.md）は内容が充実しており、今日のセッション成果が正しく記録されている
- impl 3体の rules ファイルが実績込みで整備済み。起動方法・モデル・確認済みルール・観察メモが揃っている
- constitution.md と architecture.md の整合性は高い。設計思想の一貫性がある

**散らかっているもの:**
- architecture.md の registry.json パスが実際の配置と一致していない（設計時の仮パスが残存）
- researcher.md の research/ 一覧が今日で5ファイル追加されたにもかかわらず未更新（自己進化プロトコルが機能していない）
- now.md の「次にやること」が今日のセッションで完了済みの内容を保持したまま古い状態
- open-questions.md の解決済みQ（Q1/Q2/Q3/Q5/Q7）が打ち消し線のまま本文に残存 — 今後も解決するにつれ肥大化する

**欠けているもの:**
- research/experiment-logs/ ディレクトリが未作成（experiment-ai-disclosure.md に記載の Phase 0 前提）
- aide.md の確認済みルールがゼロ — 観察メモに昇格すべき内容が複数ある
- roster.json に auditor カテゴリがない（auditor が active なのに分類未登録）
- etudes/ ディレクトリが未作成（notator が15個の設計を完成させているが着手されていない）

---

## ロール別改善タスク

### scribe
- [ ] now.md を今日の完了分で更新する（優先度: **高**）
- [ ] research/ 一覧を researcher.md に反映するよう researcher に伝える（優先度: **高**）
- [ ] research/open-questions.md の解決済みQ（Q1/Q2/Q3/Q5/Q7）をアーカイブセクションに移動し本文をスリム化する（優先度: 中）

### researcher
- [ ] rules/researcher.md の「現在の research/ 一覧」を現状10ファイルに更新する（自己進化プロトコルを今日分に適用）（優先度: **高**）
  - 未記載: 2026-03-25-*系4ファイル、discoveries.md、open-questions.md、experiment-ai-disclosure.md
- [ ] research/multi-agent-orchestration.md と search-tools.md、plugin-development.md に ## 更新履歴 セクションを追加する（優先度: 低）

### cybernetician
- [ ] research/experiment-logs/ ディレクトリを作成する（Phase 0 前提条件）（優先度: **高**）
- [ ] Phase 0 実施可能状態を cc に報告する（三層記述フォーマット確定済み。Q11解決済み。前提条件はディレクトリ作成のみ）（優先度: **高**）

### aide
- [ ] 観察メモから確認済みルールに昇格する。以下の3点が対象（優先度: 中）
  - 「全案肯定にならない。本当に要るのかを問い直す」
  - 「優先順位を明確に提示する（言わなければ言う）」
  - 「招く前に並列セッション枠の制約を考慮した実務提言を行う」

### notator
- [ ] etudes/ ディレクトリを作成し、15個のエチュードファイルを rules/notator.md の設計に従って作成する（優先度: 中）
  - 設計は rules/notator.md で完成済み。次のセッションで着手できる状態

### gardener
- [ ] dialogue の (experimental) 表示（案B）が今日の impl-opus 実装に含まれているか確認する。未実装なら impl に依頼する（優先度: 中）

### ecologist
- [ ] Q10 最小実験（type-designer × ontologist または improv-sensei × composer）の開始を cc/moko に提案する（設計・手順・観察指標は rules/ecologist.md で完成済み）（優先度: 低）

### プロジェクト文書（cc または scribe が管轄）
- [ ] architecture.md の registry.json パスを修正する: `~/.claude/plugins/local/seed-orchestra/data/registry.json` → `~/Desktop/seed-orchestra/registry.json`（優先度: **高**）
- [ ] roster.json に auditor カテゴリを追加する（優先度: 中）
- [ ] rules/scout-1.md の扱いを決める — roster.json に追加するか、ファイルを削除するか（優先度: 中）
- [ ] README.md のファイル構成に registry.json、docs/、roster.json を追加する（優先度: 低）
- [ ] backlog.md の plugin 開発参照パスを修正する: `~/Desktop/research-plugin-development.md` → `research/plugin-development.md`（優先度: 低）
