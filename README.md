# LLM Wiki

個人用の LLM Wiki です。Karpathy の LLM Wiki パターンに沿って、raw source を保持し、Codex が Markdown wiki を継続的に更新します。

Codex の運用ルールは [AGENTS.md](/c:/Users/shoei/Documents/Develop/LLM_wiki/AGENTS.md) が唯一の正です。README は人間向けの概要と使い方だけを置きます。

## 構成

```text
LLM_wiki/
├── raw/                      # 編集しない source-of-truth
│   ├── articles/             # Web記事、ブログ
│   ├── papers/               # 論文、論文テキスト
│   ├── books/                # 書籍抜粋
│   ├── videos/               # 動画 transcript、視聴メモ
│   ├── assets/               # 画像、図表、バイナリ
│   └── inbox/                # 未処理ソース
│
├── wiki/                     # Codex が保守する知識ベース
│   ├── summaries/            # ソース別要約
│   ├── entities/             # 人物、組織、ツール、製品
│   ├── concepts/             # 概念、理論、パターン
│   ├── syntheses/            # 複数ソースからの統合分析
│   ├── questions/            # 未解決の問い、調査計画
│   ├── misc/                 # lint report など
│   ├── index.md              # wiki カタログ
│   └── log.md                # 操作ログ
│
├── AGENTS.md                 # Codex 用の運用プロトコル
├── output/                   # 一時生成物。永続知識は wiki/ へ移す
├── .vscode/settings.json     # VS Code / Foam 設定
├── .gitignore
└── README.md
```

## 考え方

この wiki は3層で運用します。

- `raw/`: 真実のソース。Codex は読むが、原則として編集しない。
- `wiki/`: Codex が更新する知識ベース。要約、概念、エンティティ、統合分析を置く。
- `AGENTS.md`: Codex の作業プロトコル。Ingest / Query / Lint の詳細手順を定義する。

`output/` は作業中の一時置き場です。残す価値がある内容は `wiki/syntheses/` または `wiki/questions/` に移します。

## 初期セットアップ

VS Code で開きます。

```bash
cd LLM_wiki
code .
```

最初に読むもの:

- [AGENTS.md](/c:/Users/shoei/Documents/Develop/LLM_wiki/AGENTS.md): Codex の運用ルール
- [wiki/index.md](/c:/Users/shoei/Documents/Develop/LLM_wiki/wiki/index.md): wiki の目次
- [wiki/log.md](/c:/Users/shoei/Documents/Develop/LLM_wiki/wiki/log.md): 操作ログ

VS Code + Foam で使う前提です。`[[page-slug]]` の wikilink、backlinks、graph を使って wiki を辿ります。

## 基本ワークフロー

### Ingest

1. ソースを `raw/inbox/` または適切な `raw/*/` に置く。
2. Codex に「`raw/path/to/source.md` を ingest して」と頼む。
3. Codex は `AGENTS.md` に従い、summary/entity/concept/question/synthesis を作成または更新する。
4. Codex は `wiki/index.md` と `wiki/log.md` を更新する。
5. 生成内容を人間が確認する。

raw source は Markdown または plain text 推奨です。PDF、動画、画像などは必要に応じてテキスト化してから ingest します。

### Query

Codex に wiki への質問を投げます。

```text
wiki について質問します。
「LLM Wiki パターンの利点と通常の RAG との違いは何か？」

wiki/index.md から関連ページを探し、根拠ページを示して答えてください。
必要なら wiki/syntheses/ または wiki/questions/ に保存してください。
```

再利用できる分析は `wiki/syntheses/` に、未解決の問いは `wiki/questions/` に残します。

### Lint

定期的に wiki の健全性を確認します。

```text
AGENTS.md の Lint セクションに従って、wiki 全体をチェックしてください。
矛盾、古い情報、孤立ページ、不足リンク、根拠のない claim を確認し、
wiki/misc/lint-report-YYYY-MM-DD.md にレポートを作ってください。
```

## ページ規約

すべての wiki ページは YAML frontmatter で始めます。

```yaml
---
title: "[日本語タイトル]"
tags: [tag1, tag2]
sources: 0
created: "YYYY-MM-DD"
last_updated: "YYYY-MM-DD"
status: draft
source_refs: []
---
```

重要な factual claim は根拠を残します。

```markdown
## Claims / Evidence

- Claim: [wiki に残す主張]
  Evidence: `raw/articles/example.md`, section "[見出し]", or [[summary-page]]
  Confidence: high|medium|low
  Notes: [解釈・制約・未確認点]
```

## テンプレート

- [wiki/summaries/TEMPLATE.md](/c:/Users/shoei/Documents/Develop/LLM_wiki/wiki/summaries/TEMPLATE.md)
- [wiki/entities/TEMPLATE.md](/c:/Users/shoei/Documents/Develop/LLM_wiki/wiki/entities/TEMPLATE.md)
- [wiki/concepts/TEMPLATE.md](/c:/Users/shoei/Documents/Develop/LLM_wiki/wiki/concepts/TEMPLATE.md)
- [wiki/questions/TEMPLATE.md](/c:/Users/shoei/Documents/Develop/LLM_wiki/wiki/questions/TEMPLATE.md)

## Git 方針

- `wiki/`, `AGENTS.md`, `README.md`, `.vscode/` は git 管理する。
- `raw/**/*.md` や `raw/**/*.txt` は source-of-truth として git 管理する。
- PDF、動画、音声、画像、zip などの大容量ファイルは `.gitignore` で除外する。
- 完全削除より `status: archived` を優先する。

初期化する場合:

```bash
git init
git add .
git commit -m "Initial LLM Wiki setup"
```

## メンテナンス

- 日次: ソースを1つずつ ingest する。
- 週次: lint を実行し、矛盾・孤立・根拠不足を直す。
- 月次: `wiki/index.md` と `wiki/log.md` を見直す。

## 参考

- Original Gist: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
- Memex: https://en.wikipedia.org/wiki/Memex
