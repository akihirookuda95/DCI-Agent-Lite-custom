# Codex直接探索とDCI-Agent-Lite比較実験計画

作成日: 2026-05-22

## 目的

大量の Markdown ファイルから必要な情報と根拠ファイルを探す用途で、以下のどちらが実用的かを比較する。

1. Codex が直接 `knowledges/` を探索して回答する方法
2. Codex から DCI-Agent-Lite / `pi-mono/packages/coding-agent` を呼び出して回答する方法

評価したい観点は、正確性、速度、コスト、根拠提示の安定性、失敗しにくさである。

## 前提

検索対象は、このリポジトリ直下の `knowledges/` とする。

`knowledges/` は Git 管理対象外であり、個人用 Markdown 資料群を置くローカルディレクトリとして扱う。

DCI-Agent-Lite を使う場合は、`--cwd "$PWD/knowledges"` を指定する。

`--cwd` に `knowledges/` を指定した場合、プロンプトでは `knowledges配下` ではなく、`カレントディレクトリ配下` または `.` を検索対象として明記する。

## 比較対象

### A. Codex直接探索

Codex がこのリポジトリ上で `rg`、`sed`、`read` などを使い、直接 `knowledges/` を探索して回答する。

想定プロンプト:

```text
knowledges/ 配下のMarkdownだけを根拠にして、最近調査したサンプルプロジェクト候補を教えてください。根拠ファイルも挙げてください。
```

### B. DCI-Agent-Lite経由

Codex が以下のようなコマンドを実行し、DCI-Agent-Lite に回答させる。

```bash
set -a
source .env
set +a

uv run dci-agent-lite \
  --provider openai \
  --model gpt-5.4-nano \
  --cwd "$PWD/knowledges" \
  --max-turns 30 \
  "カレントディレクトリ配下のMarkdownファイルだけを使って答えてください。Web検索は禁止です。最近調査したサンプルプロジェクト候補は何ですか？根拠になったファイル名も挙げてください。"
```

## テスト質問

まずは以下の1問で比較する。

```text
最近調査したサンプルプロジェクト候補を教えてください。根拠ファイルも挙げてください。
```

期待される回答の中心は、ダミー例として以下のような形式にする。

```text
1. Sample Search Assistant
2. Sample RAG Evaluation Lab
3. Sample Knowledge Base CLI
4. Sample Document QA Tool
5. Sample Research Memory Agent
6. Sample Report Generator
```

期待される根拠ファイルも、公開用のダミー例として以下のように扱う。

```text
knowledges/sample-projects/sample-project-candidates.md
```

実際にローカルで実験する場合は、公開してよいダミー資料を `knowledges/` に置いてから実施する。

## 評価項目

### 正確性

- 期待される6件が漏れなく出るか。
- 余計な候補を主要候補として混ぜていないか。
- 主要候補と条件付き候補を区別できているか。

### 根拠提示

- 根拠ファイルを提示できているか。
- ファイル名だけでなく、必要なら該当セクションも説明できているか。
- 根拠のない推測をしていないか。

### 速度

- 回答までの体感時間を記録する。
- 可能なら開始時刻と終了時刻も記録する。

### コスト

- DCI-Agent-Lite の場合は `outputs/runs/<timestamp>/state.json` や `conversation.json` から usage / cost を確認する。
- Codex直接探索は正確なAPIコストが見えない可能性があるため、比較では「不明」として扱う。

### 安定性

- 空回答にならないか。
- `final.txt` が生成されるか。
- エラーや異常終了がないか。
- 検索対象パスを誤って `knowledges/knowledges` のように扱っていないか。

## 記録フォーマット

実験後、以下の形式で結果を記録する。

```text
## 実験結果

### A. Codex直接探索

- 実行日時:
- 回答:
- 根拠ファイル:
- 正確性:
- 速度:
- 問題点:

### B. DCI-Agent-Lite経由

- 実行日時:
- runディレクトリ:
- final.txt:
- 回答:
- 根拠ファイル:
- 正確性:
- 速度:
- cost:
- 問題点:

### 比較結論

- 採用候補:
- 理由:
- 次に改善すること:
```

## 判断基準

最初の実験では、DCI-Agent-Lite が Codex直接探索より明確に優れている必要はない。

ただし、以下を満たすなら DCI-Agent-Lite 専用ラッパー化を進める価値がある。

- 期待される回答を安定して出せる。
- 根拠ファイルを必ず出せる。
- 空回答などの失敗が対策可能。
- Codexに毎回探索方針を説明するより、利用体験が単純になる。

逆に、Codex直接探索の方が明らかに速く、正確で、失敗も少ない場合は、DCI-Agent-Lite化を急がず、Codex用の検索プロンプトやSkill化を先に検討する。
