# アーキテクチャ

## 全体像

```
moko（方向を示す）
  ↓
cc（Opus — 設計・判断・レビュー）
  ↓ cmux send / read-screen
メンバーたち（各 workspace で待機）
```

moko が cc に話しかけ、cc が必要に応じてメンバーに仕事を振る。

## コンポーネント

### cmux

ターミナルマルチプレクサ。各メンバーは独立した workspace で動く。
cc は `cmux send` で指示を送り、`cmux read-screen` で結果を読む。

### registry.json

チームの状態を保持する JSON ファイル。

```json
{
  "members": [
    {
      "role": "lua-sensei",
      "workspace": "workspace:4",
      "status": "active",
      "recruited_at": "2026-03-24T19:15:00+09:00",
      "notes": "Lua言語相談役"
    }
  ]
}
```

場所: `~/Desktop/seed-orchestra/registry.json`

### roster.json

メンバーのカテゴリ定義を保持する JSON ファイル。registry.json（誰が今アクティブか）とは別物。

```json
{
  "categories": {
    "lang-sensei": {
      "description": "言語専門家。各言語の設計哲学からseedを照らす",
      "members": ["lua-sensei", "julia-sensei", "..."]
    },
    "field-sensei": {
      "description": "外側（場・構造・システム）。seedという場そのものを見る",
      "members": ["gardener", "cybernetician", "toolsmith", "..."]
    }
  }
}
```

カテゴリ一覧:
- **lang-sensei** — 言語専門家 12名（lua, julia, elixir, lisp, rust, prolog, smalltalk, haskell, forth, racket, datalog, self）
- **inner-sensei** — 内側（個の内部）: somatic-sensei, type-designer
- **between-sensei** — あいだ（二者の間）: improv-sensei, poet-sensei, dialogue-sensei, composer, developmental-psych
- **field-sensei** — 外側（場・構造・システム）: gardener, cybernetician, ecologist, toolsmith, ontologist, notator, educator, category-theorist
- **observer** — 常駐オブザーバー。セッションをまたいで常にいる: buber-sensei, zizek-sensei, feynman-sensei, oka-sensei, wiles-sensei
- **staff** — 運営・支援: researcher, aide, scribe, auditor, scout-1
- **impl** — 実装者。コードを書く。タスク完了で送り出す: impl-codex, impl-opus, impl-sonnet

`/seed recruit lang-sensei` のようにカテゴリ名で一括 recruit できる。

場所: `~/Desktop/seed-orchestra/roster.json`

### rules/ ディレクトリ

**seed orchestra の記憶・結晶化システム。**

メンバーごとに `rules/<role>.md` が存在し、セッションをまたいで残る。

```
観察メモ → 試行中ルール → 確認済みルール
（観察）    （実験中）      （結晶化済み）
```

- **観察メモ**: cc が listen/check で気づいたことを記録する
- **試行中ルール**: /seed teach で送ったルール。効果を確認中
- **確認済みルール**: recruit 時に必ず伝える。これがメンバーの記憶の核
- **却下したルール**: 失敗した試みも残す。同じ過ちを繰り返さないため

**全メンバー共通ルール**: Claudeのメモリ機能（Recall/Remember等）は使わない。
全メンバーが同じDBを共有するため混在する。知識は必ず rules/ か research/ に書く。

### research/ ディレクトリ

researcher が書き、自分で更新する調査レポート群。各ファイル末尾に更新履歴がある。

cc から「自分の調査を見直して」と言えば、researcher は全ファイルを読み返し、古い情報を更新し、結果を報告する。

### /seed コマンド

cc が使うスキル。recruit / ask / check / teach / listen / dismiss / team のサブコマンドを持つ。

## ライフサイクル

```
recruit → teach → ask → listen → check
   ↑                                 |
   |    （セッション再起動で戻る）    |
   +---------------------------------+
         rules/ は残る（echo）
```

1. **recruit**: workspace を作り、Claude を起動し、挨拶する。rules/ があれば確認済みルールを教える
2. **teach**: 新しいルールを送る。試行中に記録。効果確認後、確認済みに昇格
3. **ask**: タスクを一つ送る。小さく、具体的に
4. **listen**: 全履歴を読んで要約。観察メモに特性を記録
5. **check**: 今の状態を確認
6. **dismiss**: 送り出す。rules/ は消えない

## 制約

- セッション再起動で workspace 番号がリセット → recruit し直し（ルールは残る）
- MCP サーバーはセッション途中で再接続不可
- permission は `--dangerously-skip-permissions` で回避
