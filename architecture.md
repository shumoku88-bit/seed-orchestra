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

場所: `~/.claude/plugins/local/seed-orchestra/data/registry.json`

### rules/ ディレクトリ

メンバーごとの学びを蓄積するファイル群。セッションを超えて残る。

recruit 時に「確認済みルール」を自動で一つずつ教え直す。これが seed orchestra の記憶。

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
