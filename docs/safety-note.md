# Safety Note

自由帳。

## Codexチャットからリポジトリエージェントを使う構想

このリポジトリのエージェント機構を、Codex のチャット入力欄から使えるようにしたい。

通信方法はまだ未定。CLI、Skill、MCP、ラッパースクリプト、その他の方法のどれでもよい。

イメージとしては、Codex で以下のように質問する。

```text
最近調査したサンプルプロジェクト候補を教えて。
```

その際に、同時に以下のような明示的な呼び出し指定を付ける。

```text
/use-this-repository-agent
```

この指定により、Codex 自身が通常回答するのではなく、このリポジトリのエージェント機構を内部で使う。

想定する内部処理は、`knowledges/` 配下の Markdown ファイル群を検索対象にして、DCI-Agent-Lite / `pi-mono/packages/coding-agent` を使って根拠ファイルを探し、回答を生成すること。

期待する回答は、回答本文と参照したファイルを含む形式。

例:

```text
1. Sample Search Assistant
2. Sample RAG Evaluation Lab
3. Sample Knowledge Base CLI
4. Sample Document QA Tool
5. Sample Research Memory Agent
6. Sample Report Generator

参照ファイル:
- knowledges/sample-projects/sample-project-candidates.md
```

この構想では、Codex のチャット入力欄をユーザーインターフェースとして使い、実際の検索・読解・回答生成はこのリポジトリのエージェント機構に委ねる。

### 構想のきっかけ

Codex を別のリポジトリで開いて作業していた時、そのリポジトリには `docs/` ディレクトリがあり、配下に大量の Markdown 資料があった。

人間がそれらをすべて読んで必要な情報だけを探すのは苦痛だったため、Codex に `docs/` 配下から欲しい内容と該当ファイルを探してもらうことが多かった。

この方法でも、Codex は該当ファイルと該当内容を十分に回答できていた。

ただし、より高速かつ正確な検索方法を探しており、その候補としてこのリポジトリの DCI-Agent-Lite / `pi-mono/packages/coding-agent` の仕組みを使えないか模索している。

このため、この構想の評価軸は「Codex が直接 `docs/` や `knowledges/` を探索する方法」と比べて、十分に高速・正確・根拠付きで回答できるかである。
