# マルチエージェント・オーケストレーション調査

調査日: 2026-03-24
調査者: researcher

---

## 1. Claude Code でマルチエージェントを実現する方法の全体像

2026年3月現在、Claude Code でマルチエージェントを実現する手段は大きく分けて **5つ** ある。

| 方式 | 概要 | 成熟度 |
|---|---|---|
| **A. サブエージェント（Agent tool）** | セッション内で子プロセスを生成、結果を受け取る | 安定（公式） |
| **B. Agent Teams** | 複数の独立セッションが共有タスクリストで協調 | 実験的（要フラグ） |
| **C. Git Worktree + 複数セッション** | 手動でClaude Codeを複数起動、worktreeで分離 | 安定（公式） |
| **D. Claude Agent SDK** | Python/TypeScript からプログラムでエージェントを生成 | 安定（公式） |
| **E. 外部オーケストレーター（cmux, ccswarm, tmux等）** | ターミナルマルチプレクサ等でセッションを外部管理 | コミュニティ/自作 |

### サードパーティツール

- **ccswarm** — Rust製。Claude Code/Aider/Codex をworktree分離で並列管理。TUI付き。セッション永続化、93%トークン削減
- **ruflo** — Python製。分散スウォームインテリジェンス、RAG統合
- **Multiclaude** — 複数セッションの簡易管理
- **cmux** — ターミナルマルチプレクサ。現在のseed orchestraが使用中

---

## 2. 公式の仕組み（詳細）

### A. サブエージェント（Agent tool）

**仕組み**: セッション内でClaudeが `Agent` ツールを呼び出し、子エージェントを生成。子はタスク完了後に結果を親に返す。

**定義方法**:
- ファイルベース: `.claude/agents/` または `~/.claude/agents/` にMarkdown + frontmatter
- CLI: `--agents '{JSON}'` で一時的に定義
- プラグイン: `agents/` ディレクトリに定義
- SDK: `AgentDefinition` オブジェクトをプログラムで渡す

**frontmatter 設定項目**:

```yaml
name: agent-name          # 必須。一意な識別子
description: いつ使うか     # 必須。Claudeが自動判断に使う
tools: Read, Grep, Glob   # 使えるツール（省略で全継承）
disallowedTools: Write     # 禁止ツール
model: sonnet              # モデル（sonnet/opus/haiku/inherit/フルID）
permissionMode: default    # default/acceptEdits/dontAsk/bypassPermissions/plan
maxTurns: 20               # 最大ターン数
skills: [skill-name]       # プリロードするスキル
mcpServers: [...]          # 専用MCPサーバー
hooks: {...}               # ライフサイクルフック
memory: user               # 永続メモリ（user/project/local）
background: false          # バックグラウンド実行
effort: high               # 思考レベル（low/medium/high/max）
isolation: worktree        # Git worktree分離
```

**永続メモリ** (`memory` フィールド):
- `user`: `~/.claude/agent-memory/<name>/` — 全プロジェクト共通
- `project`: `.claude/agent-memory/<name>/` — プロジェクト固有（git管理可能）
- `local`: `.claude/agent-memory-local/<name>/` — プロジェクト固有（git管理外）
- `MEMORY.md` の先頭200行が自動ロードされる
- Read/Write/Edit ツールが自動有効化

**レジューム**: 完了したサブエージェントは `SendMessage` で再開可能。コンテキスト（ツール呼出し履歴含む）は完全に保持される。

**制約**:
- サブエージェントはサブエージェントを生成できない（ネスト不可）
- 親の会話履歴は引き継がない（system promptとAgent toolのプロンプトのみ）
- 多数の結果を受け取ると親のコンテキストが膨張する

**出典**: [Create custom subagents](https://code.claude.com/docs/en/sub-agents)

### B. Agent Teams（実験的）

**仕組み**: 複数の独立したClaude Codeセッションが共有タスクリストで協調。リードが統率し、チームメイトが独立に作業。

**有効化**: `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` を設定

**アーキテクチャ**:
- **Team Lead**: メインセッション。タスク作成、チームメイト生成、結果統合
- **Teammates**: 独立セッション。自分のコンテキストウィンドウを持つ
- **Task List**: 共有タスクリスト（pending/in-progress/completed）
- **Mailbox**: エージェント間メッセージング

**表示モード**:
- **In-process**: メインターミナル内。Shift+Downでチームメイト切替
- **Split panes**: tmux/iTerm2でペイン分割。各自の出力を同時表示

**品質ゲート**:
- `TeammateIdle` フック: チームメイトがアイドルになったとき
- `TaskCompleted` フック: タスク完了時にバリデーション

**制約**:
- セッション再開で in-process チームメイトは復元されない
- 1セッション1チームのみ
- ネストなし（チームメイトはチームを作れない）
- リードは固定（交代不可）
- トークン消費が大きい

**出典**: [Agent Teams](https://code.claude.com/docs/en/agent-teams)

### C. Git Worktree

**仕組み**: `claude --worktree feature-name` で分離されたworktreeを作成し、独立セッションで作業。

**サブエージェントとの統合**: frontmatter に `isolation: worktree` を指定するとサブエージェントがworktreeで動作。変更なしなら自動クリーンアップ。

**出典**: [Git Worktrees](https://code.claude.com/docs/en/common-workflows)

### D. Claude Agent SDK

**仕組み**: Python/TypeScript からプログラムでエージェントを制御。`query()` APIでエージェントを生成し、ストリーミングで結果を受け取る。

**SDK でのサブエージェント**:
```python
from claude_agent_sdk import query, ClaudeAgentOptions, AgentDefinition

async for message in query(
    prompt="タスクの指示",
    options=ClaudeAgentOptions(
        allowed_tools=["Read", "Grep", "Agent"],
        agents={
            "worker": AgentDefinition(
                description="何をするか",
                prompt="システムプロンプト",
                tools=["Read", "Grep"],
                model="sonnet",
            )
        },
    ),
):
    ...
```

**レジュームも可能**: session_id と agent_id を保存して `resume: sessionId` で再開。

**出典**: [Agent SDK subagents](https://platform.claude.com/docs/en/agent-sdk/subagents)

### E. Plugin の agents/ ディレクトリ

プラグインの `agents/` にサブエージェントを定義して配布可能。優先順位は最低（4）。
セキュリティ上、hooks, mcpServers, permissionMode は無効。

---

## 3. cmux方式との比較

### cmux方式の特徴（現在のseed orchestra）

- cmuxでワークスペースを作成し、各ペインでClaude Codeを起動
- `cmux send` / `cmux read-screen` でテキストを送受信
- ccが手動で `registry.json` を管理
- `rules/` にメンバーごとのルールを蓄積

### 比較表

| 観点 | cmux方式 | Agent tool（サブエージェント） | Agent Teams |
|---|---|---|---|
| **セッション独立性** | 完全独立（別プロセス） | 親セッション内 | 完全独立（別プロセス） |
| **コンテキスト分離** | 完全 | 完全（結果のみ返る） | 完全 |
| **メンバー間通信** | cmux send経由（テキスト） | 不可（親経由のみ） | 直接メッセージ可能 |
| **永続メモリ** | 手動（registry.json, rules/） | 公式サポート（memory フィールド） | なし（CLAUDEmd経由のみ） |
| **ルール蓄積** | rules/ ディレクトリ | system prompt + memory | spawn prompt |
| **セッション再開** | cmuxが生きてれば可 | SendMessage でレジューム可 | 不可（再生成必要） |
| **トークンコスト** | 各セッションが独立消費 | 親+子で消費 | 各セッションが独立消費 |
| **セットアップ** | cmux必須 | 不要（組み込み） | フラグ有効化のみ |
| **AI以外のツール** | Aider, Codex等も混在可能 | Claudeのみ | Claudeのみ |
| **観察（listen）** | read-screenで全画面読める | トランスクリプトファイルで可能 | ペイン/in-processで可能 |
| **一人ずつ迎える** | 自然（recruit） | 定義ファイル追加で可能 | spawn promptで可能 |
| **相手の特性を観察** | read-screen + 観察メモ | 結果テキストのみ | メッセージ内容のみ |

### cmux方式のメリット

1. **AI以外のツールも管理可能**: Aider（DeepSeek/Gemini）、Codex も同じ仕組みで扱える
2. **完全な画面アクセス**: read-screenで相手の思考プロセスも見える
3. **seed langの精神に合致**: 「一人ずつ迎える」「listenしてからtouch」が自然に表現される
4. **フレームワーク非依存**: Claude Code のバージョンアップに左右されにくい

### cmux方式のデメリット

1. **cmux依存**: cmuxがないと動かない。インストール・設定が必要
2. **テキストベースの通信**: send/read-screenはノイズが多い。構造化データのやり取りが困難
3. **レジストリの手動管理**: registry.json をccが手動で更新する必要がある
4. **スケーラビリティ**: ペイン数に物理的制限がある
5. **MCP切断問題**: ブラウザプロセスを一人が殺すと全員に影響

---

## 4. seed orchestraの要件をcmux無しで実現できるか

### 要件の分解

seed orchestraの核心的な要件は:

1. **メンバーを一人ずつ迎える** — recruit
2. **ルールを教えて蓄積する** — teach → rules/
3. **メンバーの特性を観察する** — listen, check → 観察メモ
4. **小さく一つずつ聞く** — ask
5. **AI以外のツール（Aider等）も統合** — Codex, DeepSeek, Gemini

### 公式の仕組みで実現可能な部分

| 要件 | サブエージェント | Agent Teams | 実現度 |
|---|---|---|---|
| 一人ずつ迎える | `.claude/agents/` に追加 | spawn promptで生成 | **可能** |
| ルール蓄積 | `memory` フィールド（永続） | spawn prompt（非永続） | **サブエージェントで可能** |
| 特性の観察 | 結果テキスト（限定的） | メッセージ（限定的） | **部分的** |
| 小さく一つずつ | Agent toolで都度呼出し | タスクリストで管理 | **可能** |
| AI以外のツール | 不可（Claudeのみ） | 不可（Claudeのみ） | **不可能** |

### サブエージェント + memory でほぼ実現できる構成

```
~/.claude/agents/
├── lua-sensei.md          # Lua専門。memory: project で知見蓄積
├── plutocraft.md          # Julia/Pluto.jl専門
└── polyglot.md            # 言語設計セカンドオピニオン

.claude/agent-memory/
├── lua-sensei/
│   └── MEMORY.md          # 教えたルール、観察メモが蓄積
├── plutocraft/
│   └── MEMORY.md
└── polyglot/
    └── MEMORY.md
```

**recruit** → ファイルを作成（初回は `/agents` コマンドで対話的に）
**teach** → memory に書き込むよう指示（自動蓄積）
**listen** → トランスクリプト（`~/.claude/projects/{project}/{session}/subagents/agent-{id}.jsonl`）
**ask** → `@lua-sensei この関数を実装して` で明示的に呼び出し
**check** → `/agents` で一覧確認

### 実現できない部分

1. **AI以外のツール**: Aider（DeepSeek/Gemini）をサブエージェントとして扱うことは不可能。別のCLIツールはcmuxやtmux等の外部制御が必要
2. **リアルタイム観察**: サブエージェントの作業中の思考を「見る」ことはできない。結果のみ
3. **seed langの精神の表現力**: 「with researcher touch listen」のような seed の構文で指示を出す体験は、Agent tool の `@agent-name` 構文とは質が異なる

---

## 5. 結論：ハイブリッド推奨

### 判定

| 選択肢 | 判定 | 理由 |
|---|---|---|
| cmux に依存し続ける | △ | AI以外のツール管理には必要だが、Claude専用メンバーには過剰 |
| 完全にサブエージェントに移行 | × | Aider/Codex が使えなくなる。seed の精神が薄れる |
| **ハイブリッド** | **◎** | Claude専用はサブエージェント、外部ツールはcmux |

### 推奨構成

```
seed orchestra（ハイブリッド）
│
├── Claude 専用メンバー → サブエージェント（.claude/agents/）
│   ├── lua-sensei（Lua実装）  → memory: project で知見蓄積
│   ├── plutocraft（Julia）    → memory: project
│   └── polyglot（言語設計）    → memory: project
│
├── 外部ツール → cmux ワークスペース（従来方式）
│   ├── Aider + DeepSeek（定型実装）
│   ├── Aider + Gemini（大ファイル解析）
│   └── Codex（セカンドオピニオン）
│
└── cc（指揮）
    ├── サブエージェントは Agent tool / @mention で呼出し
    ├── cmux メンバーは cmux send / read-screen で呼出し
    └── registry.json で両方を統合管理
```

### 移行ステップ

1. **Phase 1**: Claude専用メンバー（lua-sensei, plutocraft）をサブエージェントとして定義。memory 有効化。cmuxの同名メンバーと並行運用して比較
2. **Phase 2**: 問題なければClaude専用メンバーはサブエージェント完全移行。cmuxはAider/Codex専用に
3. **Phase 3**: registry.json を拡張し、サブエージェント/cmuxの両方を管理。`/seed` コマンドを対応
4. **将来**: Agent Teams が安定したら、チーム機能の導入を検討

### 最終的な判断

**今すぐ急いで移行する必要はない。** cmux方式は動いており、seed langの精神にも合っている。ただし、Claude専用メンバーについてはサブエージェントの `memory` 機能が「ルール蓄積」を自動化してくれるため、段階的に移行する価値がある。Agent Teams は実験的で制約が多いため、現時点では見送り。

---

## 出典

- [Create custom subagents](https://code.claude.com/docs/en/sub-agents) — 公式サブエージェント全ドキュメント
- [Agent Teams](https://code.claude.com/docs/en/agent-teams) — 公式Agent Teamsドキュメント
- [Agent SDK subagents](https://platform.claude.com/docs/en/agent-sdk/subagents) — SDK でのサブエージェント定義
- [Agent SDK overview](https://platform.claude.com/docs/en/agent-sdk/overview) — SDK 概要
- [ccswarm](https://github.com/nwiizo/ccswarm) — Rust製マルチエージェントオーケストレーター
- [Shipyard: Claude Code Multi-Agent](https://shipyard.build/blog/claude-code-multi-agent/) — 2026年のマルチエージェント概況
- [Anthropic: Subagent + MCP patterns](https://winbuzzer.com/2026/03/24/anthropic-claude-code-subagent-mcp-advanced-patterns-xcxwbn/) — 公式パターン紹介

## 更新履歴

| 日付 | 内容 |
|---|---|
| 2026-03-24 | 初版作成。5方式の全体像、公式機能の詳細、cmux比較、ハイブリッド推奨の結論 |
