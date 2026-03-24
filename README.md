# seed orchestra

cmux 上で専門職チームを一人ずつ迎えて育てる仕組み。

seed lang の精神で動く — 一人ずつ迎える、小さく聞く、listen してから touch、相手を変えようとしない、echo が残る。

## 前提

- [cmux](https://github.com/anthropics/cmux) がインストール済み
- Claude Code CLI (`claude`) が使える

## セットアップ

```bash
# cmux を起動
cmux

# ccのセッションから一人ずつ迎える
/seed recruit lua-sensei
```

## 基本コマンド

| コマンド | 説明 |
|---|---|
| `/seed recruit <role>` | 新しいメンバーを迎える |
| `/seed ask <role> <質問>` | メンバーに聞く（一つずつ） |
| `/seed check [role]` | 状態を確認 |
| `/seed teach <role> <ルール>` | ルールを教える |
| `/seed listen <role>` | 全履歴を読む |
| `/seed dismiss <role>` | メンバーを送り出す |
| `/seed team` | チーム一覧 |

自然言語でも動く — 「researcherに聞いて」「結果見て」「メンバー迎えたい」。

## ファイル構成

```
seed-orchestra/
├── README.md            ← これ
├── constitution.md      ← 憲法（チームの精神・原則）
├── architecture.md      ← 仕組み
├── now.md               ← 今やっていること
├── backlog.md           ← やりたいことリスト
├── team.md              ← チーム一覧
├── rules/               ← メンバーごとの学び（セッションを超えて残る）
│   ├── lua-sensei.md
│   ├── researcher.md
│   └── ...
└── research/            ← 調査レポート（researcher が自己更新する）
    ├── plugin-development.md
    └── search-tools.md
```
