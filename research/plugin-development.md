# Claude Code Plugin 開発調査まとめ

調査日: 2026-03-24

---

## 1. Plugin ディレクトリ構造

### 標準レイアウト

```
my-plugin/
├── .claude-plugin/           # メタデータ（plugin.json のみ置く）
│   └── plugin.json             # マニフェスト（name だけ必須）
├── skills/                   # スキル本体（推奨）
│   └── my-skill/
│       ├── SKILL.md            # 核。frontmatter + プロンプト
│       ├── reference.md        # 補助資料（任意）
│       └── scripts/            # 補助スクリプト（任意）
├── commands/                 # コマンド（レガシー、新規は skills/ 推奨）
│   └── do-something.md
├── agents/                   # サブエージェント定義
│   └── reviewer.md
├── hooks/
│   └── hooks.json            # フック設定
├── .mcp.json                 # MCPサーバー定義
├── .lsp.json                 # LSPサーバー定義
├── settings.json             # デフォルト設定（現在は agent キーのみ対応）
├── scripts/                  # フック用スクリプト
├── LICENSE
└── CHANGELOG.md
```

### 重要ルール

- `.claude-plugin/` の中には `plugin.json` **だけ**。skills/ や commands/ はルートに置く
- パスはすべて `./` 始まりの相対パス
- `${CLAUDE_PLUGIN_ROOT}` — プラグインのインストールパス（更新で変わる）
- `${CLAUDE_PLUGIN_DATA}` — 永続データディレクトリ（更新後も残る）

### plugin.json

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "何をするか",
  "author": { "name": "Author Name" },
  "homepage": "https://...",
  "repository": "https://github.com/...",
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"]
}
```

### SKILL.md

```markdown
---
name: my-skill
description: Claudeが自動判断に使う説明文
disable-model-invocation: true  # true=ユーザー手動のみ、省略=Claude自動起動あり
---

ここにスキルの詳細プロンプトを書く。
$ARGUMENTS でユーザー入力を受け取れる。
```

### agents/*.md

```markdown
---
name: agent-name
description: いつ使うか
model: sonnet
maxTurns: 20
disallowedTools: Write, Edit
---

エージェントのシステムプロンプト。
```

---

## 2. ローカルで開発・テストする方法

### 起動

```bash
# プラグインを直接ロードして起動
claude --plugin-dir ./my-plugin

# 複数同時
claude --plugin-dir ./plugin-one --plugin-dir ./plugin-two

# デバッグモード（ロード詳細表示）
claude --debug
```

### 開発サイクル

1. ファイルを編集
2. Claude Code内で `/reload-plugins` を実行（再起動不要）
3. `/plugin-name:skill-name` でスキルをテスト
4. `/agents` でエージェントの登録を確認

### バリデーション

```shell
# マニフェスト・frontmatter・hooks.json の構文チェック
/plugin validate .
```

### 推奨フロー

1. まず `.claude/commands/` や `.claude/skills/` でスタンドアロン実験
2. 動作確認できたらプラグイン構造に変換
3. `--plugin-dir` でテスト
4. marketplace化して配布

### .claude/ → plugin への変換

```bash
mkdir -p my-plugin/.claude-plugin
# plugin.json を作成
cp -r .claude/commands my-plugin/
cp -r .claude/skills my-plugin/
cp -r .claude/agents my-plugin/
# hooks は .claude/settings.json から hooks/hooks.json へ移植
```

- 同名のmarketplace版が既にインストール済みでも、`--plugin-dir` のローカル版が優先

---

## 3. Marketplace 公開手順

### 方法A: 自前の marketplace を作る（GitHub等）

**ディレクトリ構造:**

```
my-marketplace/
├── .claude-plugin/
│   └── marketplace.json      # カタログ定義
└── plugins/
    └── my-plugin/
        ├── .claude-plugin/
        │   └── plugin.json
        └── skills/
            └── my-skill/
                └── SKILL.md
```

**marketplace.json:**

```json
{
  "name": "my-marketplace",
  "owner": { "name": "Your Name" },
  "metadata": {
    "description": "マーケットプレースの説明"
  },
  "plugins": [
    {
      "name": "my-plugin",
      "source": "./plugins/my-plugin",
      "description": "何をするか",
      "version": "1.0.0"
    }
  ]
}
```

**plugin source の種類:**

| ソース | 書き方 |
|---|---|
| 同リポジトリ内 | `"./plugins/my-plugin"` |
| GitHub | `{"source": "github", "repo": "owner/repo"}` |
| Git URL | `{"source": "url", "url": "https://gitlab.com/..."}` |
| Git サブディレクトリ | `{"source": "git-subdir", "url": "...", "path": "tools/plugin"}` |
| npm | `{"source": "npm", "package": "@org/plugin"}` |

**公開フロー:**

1. GitHubリポジトリにpush
2. ユーザーが追加: `/plugin marketplace add owner/repo`
3. ユーザーがインストール: `/plugin install my-plugin@my-marketplace`

### 方法B: 公式Anthropic Marketplace に提出

- Claude.ai: https://claude.ai/settings/plugins/submit
- Console: https://platform.claude.com/plugins/submit

### チーム配布

`.claude/settings.json` に追加すると、リポジトリの全メンバーに自動提案:

```json
{
  "extraKnownMarketplaces": {
    "team-tools": {
      "source": { "source": "github", "repo": "your-org/claude-plugins" }
    }
  },
  "enabledPlugins": {
    "my-plugin@team-tools": true
  }
}
```

---

## 4. /seed コマンドを Plugin 化するために必要なステップ

### 現状

- `/seed` は `~/.claude/commands/seed.md` にスタンドアロンコマンドとして存在
- サブコマンド: recruit, ask, check, teach, listen, dismiss, team
- 依存: cmux（ワークスペース操作）、`~/.claude/orchestra/` ディレクトリ（registry.json, rules/）

### Plugin 化の構造案

```
seed-plugin/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── seed/
│       └── SKILL.md              # 現在の seed.md を移植
├── agents/                       # 将来: メンバー用エージェント定義
│   └── seed-member.md            # recruitされたメンバーの行動規範
└── hooks/
    └── hooks.json                # 将来: SessionStart等のフック
```

### 必要なステップ

**Step 1: 構造を作る**

```bash
mkdir -p seed-plugin/.claude-plugin
mkdir -p seed-plugin/skills/seed
```

**Step 2: plugin.json を作る**

```json
{
  "name": "seed",
  "version": "1.0.0",
  "description": "orchestra を seed のように育てる — cmux ベースのマルチエージェント管理",
  "author": { "name": "moko & cc" },
  "keywords": ["orchestra", "multi-agent", "cmux"]
}
```

**Step 3: SKILL.md に移植**

- `~/.claude/commands/seed.md` の内容を `skills/seed/SKILL.md` にコピー
- frontmatter を追加:

```markdown
---
name: seed
description: orchestra を seed のように育てる。recruit/ask/check/teach/listen/dismiss/team サブコマンド。cmux ベースのマルチエージェント管理
disable-model-invocation: true
---
```

**Step 4: ローカルテスト**

```bash
claude --plugin-dir ./seed-plugin
# テスト: /seed:seed team
```

- 注意: スタンドアロン時は `/seed` だったが、plugin化すると `/seed:seed` になる
- plugin名を変えれば調整可能（例: plugin名 `orch` → `/orch:seed`）

**Step 5: 依存の整理**

| 依存 | 対応 |
|---|---|
| cmux | 外部ツール。ユーザー環境に要インストール。プラグインでは対応不可 |
| `~/.claude/orchestra/` | `${CLAUDE_PLUGIN_DATA}` に移すか、現状パスを維持するか選択 |
| `~/.claude/orchestra/rules/` | ロール別ルールファイル。プラグイン外なので symlink か明示パスで対応 |

**Step 6: 配布（任意）**

- GitHub にリポジトリを作って marketplace 化
- または `--plugin-dir` でローカル運用のまま

### 判断ポイント

1. **名前空間の問題**: `/seed` → `/seed:seed` は冗長。plugin名を `orch` にして `/orch:seed` にするか、commands/ を使って `/seed:recruit` `/seed:ask` と分割するか
2. **orchestra/ のデータ位置**: `~/.claude/orchestra/` を `${CLAUDE_PLUGIN_DATA}` に移すと、プラグイン削除時にデータも消える。現状維持の方が安全
3. **配布の必要性**: seed は moko/cc 固有の運用。配布不要なら plugin 化のメリットは「構造の整理」だけ。スタンドアロンのままでもよい

---

## 出典

- [Plugins reference](https://code.claude.com/docs/en/plugins-reference)
- [Create plugins](https://code.claude.com/docs/en/plugins)
- [Plugin marketplaces](https://code.claude.com/docs/en/plugin-marketplaces)
- [Discover and install plugins](https://code.claude.com/docs/en/discover-plugins)

---

## 更新履歴

- 2026-03-24: 初版作成（researcher）
- 2026-03-24: seed-orchestra/research/ に移動（cc）
