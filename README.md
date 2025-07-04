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
svelte-chart-data/
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
├ docker/
│ └ front
│   └ Dockerfile
├ docker-compose.yml
├ package.json
├ package-lock.json
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

```svelte.config.js
-import adapter from '@sveltejs/adapter-auto';
+import adapter from '@sveltejs/adapter-node';
```

### Dockerfileの作成

```Dockerfile
FROM node:20-alpine

# 作業ディレクトリ作成
WORKDIR /app

# package.jsonとpackage-lock.jsonをコピー（キャッシュを効かせるため）
COPY package*.json ./

# 依存関係をインストール
RUN npm install  # 開発環境用：新しく依存パッケージを追加したいとき（lockファイルがなくても実行可）
# RUN npm ci         # 本番環境用：package-lock.jsonに従って完全に同じ依存環境を再現

# Skeleton Labs 関連をインストール
RUN npm install -D @skeletonlabs/skeleton @skeletonlabs/tw-plugin

# TailwindCSS を手動で追加（対話なし）
RUN npm install -D tailwindcss postcss autoprefixer \
    && npx tailwindcss init tailwind.config.cjs -p

# 残りのソースコードをコピー
COPY . .

# ビルド
RUN npm run build

# ポート指定（任意）
EXPOSE 3000

# アプリ実行
# CMD ["npm", "run", "preview"]
# CMD ["node", "./build"]
CMD ["npm", "run", "dev", "--", "--host"]
```

### docker-compose.ymlの作成

```docker-compose.yml
version: '3.8'

services:
  frontend:
    image: svelte-chart-datap
    build:
      context: .
      dockerfile: docker/front/Dockerfile
    environment:
      - NODE_ENV=development
      - PORT=5173
    ports:
      - 5173:5173
    volumes:
      - ./src:/app/src
      - ./static:/app/static
      - ./vite.config.js:/app/vite.config.js
      - ./tsconfig.json:/app/tsconfig.json
      - ./svelte.config.js:/app/svelte.config.js
```
