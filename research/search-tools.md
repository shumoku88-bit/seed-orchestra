# Claude Code 検索ツール調査レポート

調査日: 2026-03-24

---

## 1. WebSearch / WebFetch の仕組み

### WebSearch

- **バックエンド**: Brave Search API（サーバーサイド実行）
- **流れ**: クエリ → Anthropicサーバー → Brave Search API → 結果返却
- **返るデータ**: タイトル + URL のみ（暗号化コンテンツは破棄される）
- **ページの中身は読めない** → WebFetch との併用が必要
- **パラメータ**: `query`（必須）、`allowed_domains`、`blocked_domains`、`user_location`
- **コスト**: API経由で $10/1,000検索 + トークン代
- **制限**: AWS Bedrock / Google Vertex AI では使えない

### WebFetch

- **実行場所**: ローカル（自分のIPからAxiosでHTTP取得）
- **JS実行**: なし（SPAは読めない）
- **変換**: Turndown で HTML → Markdown、100KBで切り詰め
- **二次処理**: Claude 3.5 Haiku が要約（信頼ドメイン約80件は詳細引用許可、それ以外は125文字制限）
- **キャッシュ**: 15分間LRU、50MB上限
- **コスト**: トークン代のみ（WebSearchの約1/4）
- **高速パス**: Content-Type が text/markdown かつ 100K未満ならHaiku処理をスキップ

### 両者の役割分担

| | WebSearch | WebFetch |
|---|---|---|
| 何をする | 検索結果のメタデータ取得 | ページ本文の取得・要約 |
| 実行場所 | Anthropicサーバー | ローカル |
| 返るもの | タイトル + URL | Markdown化された本文 |
| JS対応 | - | なし |

---

## 2. 検索精度を上げるコツ

### クエリの書き方

- **英語で検索する**: Brave Search は英語の方がヒット精度が高い。日本語で頼む場合も「英語で検索して」と添える
- **具体的に書く**: 技術名・比較軸・年を入れる（例: "Rust tokio vs async-std performance 2025"）
- **ドメイン指定**: `allowed_domains` で公式ドキュメントに絞る、`blocked_domains` でノイズ除外

### ワークフロー

- **WebSearch → WebFetch の2段階**: WebSearchでURL特定 → WebFetchで中身を読む、を明示的に指示する
- **一度に聞きすぎない**: 1トピックずつ検索→読む→次、の方が各クエリの精度が上がる
- **Brave Search MCP を優先**: 内蔵WebSearchはタイトル+URLのみだが、MCPはスニペット付きで返る。当たりをつけやすい

---

## 3. 試す価値のある MCP サーバー一覧

### 検索系

| 名前 | 何ができるか | インストール |
|---|---|---|
| **Brave Search** | Web検索 + ローカル検索。スニペット付き。pagination対応 | `npx @anthropic-ai/mcp-server-brave-search` + `BRAVE_API_KEY` |
| **Google Search** | Google Custom Search API経由。日本語に強い | `npx @anthropic-ai/mcp-server-google-search` + Google API Key + CSE ID |
| **Exa** | セマンティック検索。意味ベースで類似ページを探せる | `npx @anthropic-ai/mcp-server-exa` + `EXA_API_KEY` |
| **Tavily** | AI特化検索。結果に要約が付く。LLMとの相性が良い | `npx tavily-mcp-server` + `TAVILY_API_KEY` |

### ページ取得・スクレイピング系

| 名前 | 何ができるか | インストール |
|---|---|---|
| **Fetch** | シンプルなページ取得。WebFetchの代替 | `npx @anthropic-ai/mcp-server-fetch` |
| **Firecrawl** | JS実行付きページ取得。SPA対応。WebFetchの弱点を補う | `npx firecrawl-mcp` + `FIRECRAWL_API_KEY` |
| **Puppeteer** | ヘッドレスブラウザ。ログイン後ページや動的コンテンツ取得 | `npx @anthropic-ai/mcp-server-puppeteer` |

### 特化型

| 名前 | 何ができるか | インストール |
|---|---|---|
| **GitHub** | リポジトリ・Issue・コード検索。技術リサーチに直結 | `npx @anthropic-ai/mcp-server-github` + `GITHUB_TOKEN` |

### 現在の環境状況

- Brave Search MCP: **稼働中**（API Key設定済み、レート制限 1req/sec・15,000req/月）
- その他: 未インストール（必要に応じて追加可能）

---

## 更新履歴

- 2026-03-24: 初版作成（researcher）
- 2026-03-24: seed-orchestra/research/ に移動（cc）
