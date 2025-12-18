# zenn

## Zenn CLI のインストール（導入）

このリポジトリでは `npx zenn ...` のように **npx経由で実行**できます（グローバルインストール不要）。

### すぐ使う（インストール不要）

```bash
npx zenn --version
```

### プロジェクトにインストールする（推奨）

```bash
npm install --save-dev zenn-cli
```

以後は以下で実行できます。

```bash
npx zenn --version
```

（初回だけ）Zenn用のディレクトリや設定が無い場合は初期化します：

```bash
npx zenn init
```

## Zenn CLI で下書き記事（ポスト）を作る

### 前提
- Node.js が入っていること
- このリポジトリ配下に `articles/` があること（本リポジトリは該当）

### 下書き記事を新規作成

```bash
npx zenn new:article
```

`articles/` 配下に新しい Markdown が生成されます。下書きにするには Front Matter の `published` を `false` にします（新規生成時点で `false` の場合もあります）。

```yaml
---
title: "記事タイトル"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["DevOps"]
published: false
---
```

### ローカルプレビュー

```bash
npx zenn preview
```

### 公開する
- `articles/*.md` の Front Matter を `published: true` に変更
- 変更をリポジトリに反映（運用に合わせて push / PR）