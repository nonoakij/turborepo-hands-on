---
title:  What is Turborepo
date: 2025-03-29
layout: cover
---

# What is Turborepo

---

## 🗂 アジェンダ

<div class="mt-8">

1. 🎯 今日のゴール
2. 🌍 世界はモノレポで何をしたいのか？
3. 🚨 モノレポの課題
    1. 🔧 CI/CDの課題（例）
4. ⚡ Turborepo がどのように解決するのか？
    1. 🏃 Turborepo の実行戦略
    2. 🧠 Turborepo のキャッシュ戦略
5. 💻 Hands-On

</div>

---

## 🎯 今日のゴール

<div class="flex items-center justify-center h-full">

### Turborepoの思想と役割を理解し、導入イメージを掴む

</div>

---

## 🌍 世界はモノレポで何をしたいのか？

<div class="mt-8">

- フロントエンドとバックエンドを同じリポジトリで管理したい
  - APIの型をフロントエンドの型に流用したい！！
    - OpenAPI があるじゃん!?
      - OpenAPI yamlを変更 -> SDK 生成 -> Publish -> Front-end repo install の流れになる
      - SDK のバージョン管理が必要
        - SDK のバージョンの更新漏れると、APIの変更に気づかず型エラーも出ない 🥺
          - Runtime Error 👹
- 共通パッケージを複数アプリで使い回したい
  - UIコンポーネントやユーティリティ関数を共通化したい
  - lint や typescript の設定を共通化したい
    - Private npm registry (npm Private Packages, JFrog, and more) を使う
      - それぞれのリポジトリでバージョン管理が必要 🤮
- ...

</div>

---

## 🚨 モノレポの課題

<div class="mt-8">

- 小規模では問題ないが、規模が大きくなると…
  - ビルド時間の増加
  - 影響範囲の特定が難しい
  - CIが重くなる
  - 「全部走らせる」戦略は破綻する


</div>

---

## 🔧 CI/CDの課題（例）

<div class="mt-8">

- 全パッケージを毎回ビルド → 非効率
- 並列化されていないとビルドが長時間に
- 「前回と同じタスクなのにまたビルド」
<img class="h-80 mx-auto mt-4 object-contain" src="https://turbo.build/_next/image?url=%2Fimages%2Fdocs%2Fslow-tasks-light.png&w=828&q=75" />

</div>

---

## ⚡ Turborepoがどう解決するのか？

<div class="mt-8">

- 最小限のタスク実行
- キャッシュ戦略による高速化
- 依存関係を理解した実行順の最適化
<img class="h-80 mx-auto mt-4 object-contain" src="https://turbo.build/_next/image?url=%2Fimages%2Fdocs%2Fturborepo-tasks-fast-light.png&w=828&q=75" />
</div>

---

## 🏃 Turborepoのpipeline

<div class="mt-8">

`turbo.json` でタスクの依存関係を定義できます。
細かい説明は省略しますが、以下のようなことができます。

```json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    }
  }
}
```

- `^build` → 親依存のbuildが終わってから実行
- `outputs` → 出力ファイルを定義してキャッシュの対象に

</div>

---

## 🧠 Turborepoのキャッシュ戦略

<div class="mt-8">

- 実行ログ・ファイル出力・依存関係を元にキャッシュ
- 同じ入力 → 同じ出力 → 再実行スキップ
- `node_modules/.cache/turbo` にローカルキャッシュ
- Remote Cacheも利用可能（Vercel or 自前）

</div>

---

## 🛠 Hands-Onでやること

<div class="mt-8">

- 既存の npm プロジェクトのつらみを体験する
- 既存の npm workspace プロジェクトに TurboRepo を導入する
- `turbo` パッケージの追加
- `turbo.json` 作成
- 実行して違いを見る
- Remote Cacheを試す

</div>

---

## 💻 Let's Start Hands-On!

<div class="mt-8">

### Clone the repository
```bash
# Using GitHub CLI
gh repo clone nonoakij/turborepo-hands-on -- --branch trailhead

# Using Git
git clone -b trailhead https://github.com/nonoakij/turborepo-hands-on.git
```

</div>
<div class="mt-8">

### Install dependencies and start the server

```bash
cd turborepo-hands-on
npm install
npm run dev
```

</div>

---

## ビルドしてみる
<div class="mt-8">

```bash
npm run build
```
</div>

---

## turbo をインストールする
<div class="my-8">

```bash
npm install turbo --save-dev
```

</div>

## turbo.jsonを作成する

<div class="mt-8">

```bash
touch turbo.json
```

中身は[これ](https://raw.githubusercontent.com/nonoakij/turborepo-hands-on/refs/heads/main/turbo.json)を使ってください

<https://raw.githubusercontent.com/nonoakij/turborepo-hands-on/refs/heads/main/turbo.json>

</div>

---

## scriptsを変更する
<div class="mt-8">

```json
...
  "scripts": {
    "build": "turbo run build",
    "clean": "turbo run clean",
    "dev": "turbo run dev",
    "format": "prettier --write \"**/*.{ts,tsx,md}\"",
    "lint": "turbo run lint",
    "test": "turbo run test",
    "check-types": "turbo run check-types",
    "gen-graph": "turbo build --graph=docs/graph.png"
  },
...
```

<https://github.com/nonoakij/turborepo-hands-on/blob/main/package.json>

</div>

---

## turbo で dev と build を実行する
<div class="mt-8">

```bash
npm run dev
```
</div>

<div class="mt-8">

```bash
npm run build
```
</div>

---

### Deps graph を生成する

<div class="mt-8">

```bash
npm run gen-graph
```

</div>
<div class="mt-8">

> [!WARNING]
> graphviz が必要です。
> ```bash
> brew install graphviz
> ```

</div>
---

### 実行の依存を確認する
<div class="mt-8">

```sh
npm run lint -- --dry --filter='admin'
```
</div>

---

### Remote Cacheを試す
<div class="mt-8">

```sh
npx turbo login
turbo link
```
</div>
---

## 🙌 まとめ
<div class="mt-8">

- Turborepo はモノレポのビルドを最適化するツール
- モジュールの管理は npm workspace で行う
  - yarn, pnpm, bun(Beta) でもよい
</div>
---
layout: center
---

# ありがとうございました 🎉
