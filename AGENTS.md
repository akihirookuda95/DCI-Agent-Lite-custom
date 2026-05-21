# AGENTS.md

## Project Rules

このリポジトリでは、検討・意思決定・実装メモを以下の3つのファイルに分けて管理する。

- `docs/safety-note.md`
- `docs/decision-note.md`
- `docs/implementation-note.md`

### `docs/safety-note.md`

`safety-note.md` は自由帳として使う。

Web検索で調査した結果、コードベースを解析した結果、任意のデータセットで検証した結果、不安なこと、思いついたこと、仮説、未確定の論点など、何でも書いてよい。

このファイルは、まだ決定していない情報や、あとで議論・整理するための材料を置く場所である。

### `docs/decision-note.md`

`decision-note.md` は決まったことを記録する。

Codex とユーザーの議論で決まったことを書く。対象は、設計方針、要件定義、開発の進め方、実装方針、テスト方針、技術選定など、決定済みの内容全般とする。

`safety-note.md` を参照しながら議論して決まったことがある場合は、決まったことだけを抽出して `decision-note.md` に書く。

### `docs/implementation-note.md`

`implementation-note.md` は、Codex が実装時に参照するメモを書く。

Codex が実装する時は、原則として `safety-note.md` や `decision-note.md` ではなく、`implementation-note.md` だけを見て実装する。

理由は、実装時に多すぎるコンテキストを含めると、ユーザーの意図とずれた実装になる可能性があるためである。実装に必要な情報は省略せずに書く一方で、甘い内容や未整理の内容を何百行も書かないようにする。

`implementation-note.md` は、実装段階に進む前に `safety-note.md` と `decision-note.md` から実装に必要な情報だけを抽出して作成・更新する。目安として500行を超えないようにする。

### 情報の反映順

3つのファイルの基本的な情報の流れは、次の方向とする。

```text
safety-note.md -> decision-note.md -> implementation-note.md
```

Codex とユーザーが協働している時は、調査結果、思いつき、解析結果、不安点などを `safety-note.md` に書く。

その場で決まったことがあれば `decision-note.md` に書く。`safety-note.md` から内容を移す場合は、決まったことだけを抽出する。

実装に進む前に、`safety-note.md` と `decision-note.md` から実装に必要な情報だけを `implementation-note.md` に抽出する。

### 逆方向の反映禁止

原則として、次の方向には内容を抽出して書き出さない。

```text
implementation-note.md -> decision-note.md -> safety-note.md
```

逆方向に内容が流れると、それぞれのファイルの責務が曖昧になるためである。情報整理は、なるべく `safety-note.md` から `decision-note.md`、さらに `implementation-note.md` へ進める。

## Implementation Rules

実装時は、以下のコード構成・コメント・関数設計ルールに従う。

### コード構成

メインの処理はファイルの先頭部分に書く。

ヘルパー関数などのサブの処理は、メインの処理の下に書く。

メインの処理には、メインの処理であることを日本語コメントで明記する。

サブの処理には、サブの処理であることを日本語コメントで明記する。

### コメント

すべてのクラスと関数には、日本語で処理の概要コメントを書く。

重要な処理や読むのが難しい処理には、日本語コメントを書いて可読性を上げる。

コメントは、処理の意図や判断理由を理解しやすくするために書く。

### 関数設計

1つの関数の責務は1つにする。

1つの関数が複数の責務を持つ場合は、ヘルパー関数に切り出す。

1つの関数内では、処理の抽象度を揃える。

処理の抽象度が揃わない場合も、ヘルパー関数に切り出す。
