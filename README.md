# svelte-chart-data
Docker上で動作するSvelte フロントエンドアプリ

#### 参考
- [Svelte公式ドキュメント](https://svelte.jp/docs/kit/introduction)

## 前提
下記はインストール済みとして割愛します。

* Node.js
* Docker Desktop

## SvelteKitプロジェクトを作成

```
npm create svelte@latest svelte-chart-data
cd svelte-chart-data
npm install
```

### `npm create` 実行時のオプション指定
- Which Svelt app template? : Skelton
- Add type checking with TypeScript? : Yes
- Select additional options. :
  - Add ESLint for code linting : on
  - Add Prettier for code formatting : on
  - Add Playwright for browser testing : on
  - Add Vitest for unit testing : on

### パッケージ構成

```
my-project/
├ src/
│ ├ lib/
│ │ ├ server/
│ │ │ └ [your server-only lib files]
│ │ └ [your lib files]
│ ├ params/
│ │ └ [your param matchers]
│ ├ routes/
│ │ └ [your routes]
│ ├ app.html
│ ├ error.html
│ ├ hooks.client.js
│ ├ hooks.server.js
│ └ service-worker.js
├ static/
│ └ [your static assets]
├ tests/
│ └ [your tests]
├ package.json
├ svelte.config.js
├ tsconfig.json
└ vite.config.js
```

## Tailwind CSSのインストール
CSSフレームワークで、CSSを書くことなく、汎用クラスを組み合わせてデザインを構築する。
対話型で依存関係やファイルを書き換えるスクリプトなので、事前にインストールしておく。

```
npx svelte-add@latest tailwindcss
npm install
```

## Docker環境構築

### Nodeアダプタを適用

```
npm install @sveltejs/adapter-node -D
```

