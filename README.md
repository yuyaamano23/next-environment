# 環境構築

参考にさせていただいた神記事。おおまかパクリです。<br>
[2020 年師走における Next.js をベースとしたフロントエンドの環境構築](https://zenn.dev/higa/articles/d7bf3460dafb1734ef43)

<br>
<br>
<br>

## 1. プロジェクトの作成

### 1.1 ルートとなるプロジェクトの作成

```
$ mkdir next-project
```

**Next.js のプロジェクトをセットアップ**<br>
Next.js で v9.1 から src に pages などを入れることができるらしいンゴ。<br>のちのち複雑になる可能性を考えて src ディレクトリへ移動しておくことにする。

```
$ yarn create next-app .
```

### 1.2 ディレクトリを移動

```
$ mkdir src
$ mv pages/ src/pages
$ styles/ src/styles
```

<br>

## 2. TypeScript の導入

### 2.1 設定ファイルのセットアップ

これで tsconfig.json の中身と next-env.d.ts が新規作成される

```
$ touch tsconfig.json
$ yarn add --dev typescript @types/react @types/node
$ yarn dev
```

### 2.2 TS ファイルに変換

```zsh
$ find src/pages -name "_app.js" -or -name "index.js" | sed 'p;s/.js$/.tsx/' | xargs -n2 mv
$ find src/pages/api -name "*.js" | sed 'p;s/.js$/.ts/' | xargs -n2 mv
```

### 2.3 App コンポーネントを TypeScript に対応させる

[変更後の\_app.tsx](https://github.com/yuyaamano23/next-typescript-todo/commit/f7b9027ae2fa4f2d6c9b7f4343d2c60884a5220a)

### 2.4 page コンポーネントを TypeScript に対応させる

[変更後の index.tsx](https://github.com/yuyaamano23/next-typescript-todo/blob/d468c45c23622269f939bbc0ebb02897ff5252e8/src/pages/index.tsx)

### 2.5 API フォルダの削除

フロンドエンドで API を作成する予定はないので削除!!!(デスノート:みかみ)

### 2.6 src/types フォルダの作成

TypeScript の Type 置き場を作っておく
<br>

## 3. Document コンポーネントを追加

Document コンポーネント を使うと、初期状態だと自動で追加される html タグ, や body タグに変更を加えることができます。

```
$ touch src/pages/_document.jsx
```

コード参照<br>
↓<br>
[追加した Document.tsx](https://github.com/yuyaamano23/next-typescript-todo/commit/0922e33cd3cc72e72e13ddf505eae3524b188838)

<br>

## 4. ベース URL を設定

src に pages ディレクトリなどを移動しましたので、src をベース URL に設定するよー。

### 4.1 TypeScript の設定を変更

TypeScript の設定にモジュールインポートのベース URL を追記。<br>
_tsconfig.json_ ↓

```json
{
  "compilerOptions": {
    // ベース URL を追加
    "baseUrl": "src"
  }
}
```

各コンポーネントのモジュールインポートの指定を、ベース URL 指定に変更するよー。

```zsh
$ sed -i '' -e 's/..\/styles/styles/' src/pages/_app.tsx
$ sed -i '' -e 's/..\/styles/styles/' src/pages/index.tsx
```

<br>

## 5. スタイルの設定

Next.js はデフォルトで CSS Modules に対応している。今回は取りあえす CSS Modules を採用する。<br>
揺れ動く感情の中 CSS Module の採用を後押ししてくれた神記事 ↓<br>
[styled-components（CSS in JS）をやめた理由と、不完全な CSS Modules を愛する方法](https://qiita.com/jagaapple/items/7f74fc32c69f5b731159)

### 5.1 SASS のインストール

```
$ yarn add -D sass
```

### 5.2 SASS ファイルに変換

`src/styles`ディレクトリ内の CSS ファイルを SASS ファイルに変換するよー。

```zsh
$ find src/styles -name "*.css" | sed 'p;s/.css$/.scss/' | xargs -n2 mv
```

### 5.3 SASS ファイルを読み込むように変更

```zsh
$ sed -i '' -e 's/\.css/\.scss/' src/pages/_app.tsx
$ sed -i '' -e 's/\.css/\.scss/' src/pages/index.tsx
```

<br>

### 5.4 styles ディレクトリ内の構造を工夫する

```
$ mkdir src/components
$ mkdir src/styles/layouts
$ touch src/styles/variables.scss
```

適宜ファイルの`import`を変更してください。

<br>

参考記事 →[Next.js に CSS Modules を導入する](https://zenn.dev/catnose99/scraps/5e3d51d75113d3)

`styles/global.scss`<br>

- ここにグローバルに（アプリ全体で）読み込みたいスタイルを書く。<br>
- \_app.tsx で import styles/global.scss するだけで読み込み設定は完了。

`styles/components`

- コンポーネント用の.module.scss を入れてく

`styles/layouts`<br>

- レイアウト用のコンポーネントは layouts ディレクトリに入れる

`styles/variables.scss`<br>

- scss ファイルで使いまわしたい変数をここに入れる

<br>

## 6. デフォルト CSS の追加

ブラウザ間の誤差を吸収する為や、スタイルを追加しやすくする為に、デフォルト CSS はあります。<br>
今回は、[sanitize.css](https://csstools.github.io/sanitize.css/)を採用します。

### 6.1 インストール

```
$ yarn add -D sanitize.css
```

### 6.2 App コンポーネントを変更

デフォルト CSS を全体に適応する為に、App コンポーネントで sanitize.css を読み込みます。

## 7. 静的解析と整形のツールを追加

コードの静的解析と整形の為に、以下のツールを追加します。

- [ESLint](https://eslint.org/)
- [Prettier](https://prettier.io/)

### 7.1 Prettier の追加

#### 7.1.1 インストール

```
$ yarn add -D prettier
```

#### 7.1.2 設定ファイルの追加

```
$ touch .prettierrc.js
```

以下追加

```js
module.exports = {
  "semi": false,
  "arrowParens": "always",
  "singleQuote": true
)
```

### 7.2 ESLint の追加

#### 7.2.1 インストール

```
$ yarn add -D eslint eslint-plugin-react
$ yarn add -D eslint-config-prettier eslint-plugin-prettier
$ yarn add -D @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

#### 7.2.2 ESLint の設定ファイルを作成

```
$ touch .eslintrc.js
```

以下記述

```js
module.exports = {
  ignorePatterns: ['!.eslintrc.js', '!.prettierrc.js'],
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:@typescript-eslint/eslint-recommended',
    'plugin:prettier/recommended',
    'prettier/@typescript-eslint',
  ],
  plugins: ['@typescript-eslint', 'react'],
  parser: '@typescript-eslint/parser',
  env: {
    browser: true,
    node: true,
    es6: true,
    jest: true,
  },
  parserOptions: {
    sourceType: 'module',
    ecmaFeatures: {
      jsx: true,
    },
  },
  settings: {
    react: {
      version: 'detect',
    },
  },
  rules: {
    // 必要に応じてルールを追加
    'react/prop-types': 'off',
    'react/react-in-jsx-scope': 'off',
    '@typescript-eslint/no-explicit-any': 'off',
  },
}
```

#### 7.2.3 Next.js の設定ファイルを修正

Next.js の設定ファイルの先頭に eslint-disable を設定する。

```js
/* eslint-disable
   @typescript-eslint/no-var-requires
*/
```

#### 7.2.4 gitignore にリポジトリで管理しないファイルを追記

```zsh
# ESLint のキャッシュファイルを追加
.eslintcache
```

#### 7.2.5 ESLint を実行する NPM スクリプトを追記

`package.json`

```json
{
  "scripts": {
    "lint": "eslint --ext .js,.jsx,.ts,.tsx --ignore-path .gitignore ."
  }
}
```

問題なく Lint が通るか確認します。

```
$ yarn lint
```

**もし通らない場合は fix オプションをつけて `yarn lint --fix` と実行することで、整形可能なものに関しては自動整形することができます。**

### 7.3 VSCode の設定

VSCode の設定ファイルを追加します。

```
$ mkdir .vscode
$ touch .vscode/settings.json
```

以下を設定することファイルの**保存時に自動整形**することができます。

`settings.json`

```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact"
  ]
}
```

## 8. テストの追加

今回は Jest （Unit テスト）を採用

### 8.1 Jest を追加

```zsh
# Jest 関連モジュールをインストール
$ yarn add -D jest identity-obj-proxy

# Jest の TypeScript に関するモジュールをインストール
$ yarn add -D ts-jest @types/jest
```

### 8.2 設定ファイルの追加

```
$ touch jest.config.js
```

```js
module.exports = {
  preset: 'ts-jest',
  roots: ['<rootDir>/src'],
  moduleNameMapper: {
    // CSS モックをモックする設定
    '\\.(css|scss)$': 'identity-obj-proxy',
    // pages と components ディレクトリのエイリアスを設定（必要であれば他のディレクトリも追加）
    '^(pages|components)/(.+)': '<rootDir>/src/$1/$2',
  },
  moduleFileExtensions: ['ts', 'tsx', 'js', 'jsx', 'json'],
  globals: {
    'ts-jest': {
      tsconfig: {
        jsx: 'react',
      },
    },
  },
}
```

### 8.3 NPM スクリプトの追加

テストを実行する為の NPM スクリプトを追記します。<br>
`package.json`

```json
{
  "scripts": {
    "test": "jest src/__tests__/.*/*.test.tsx?$"
  }
}
```

### 8.4 テストモックの追加

適当なテストを書いてみます。

```zsh
$ mkdir src/__tests__
$ touch src/__tests__/Sample.test.tsx
```

`Smaple.test.tsx`

```js
/// <reference types="jest" />

import React from 'react'
import Home from 'pages/index'

it('Home ページコンポーネントが存在している', () => {
  expect(Home).toBeTruthy()
})
```

問題なくテストが動作しているか確認します。

```zsh
$ yarn test
```

エラーにならずに書いたテストをパスしていれば問題ないです。

## 9. フックスクリプトの追加

リポジトリへのコミットやプッシュの際に、事前に Lint やテストを自動実行できるようにします。<br>
これによりプロジェクトを健全に保つことができます。

### 9.1 lint-staged の追加

[lint-staged](https://github.com/okonet/lint-staged) は Git のステージに上っているファイルだけを Lint の対象にすることができるツールです。

#### 9.1.1 インストール

```zsh
$ npx mrm lint-staged
```

#### 9.1.2 NPM スクリプトに追加

lint-staged を実行する NPM スクリプトを追記します。

```json
{
  "scripts": {
    "lint-staged": "lint-staged"
  }
}
```

### 9.2 husky の追加

## なんかうまく行かないですンゴ

https://www.youtube.com/watch?v=R35LJL6a-p0&t=1762s

[husky](https://typicode.github.io/husky/#/) はコミットやプッシュの前にテストなどを実行して、失敗したら止めることができる Git hooks を簡単に設定することがツールです。

#### 9.2.1 インストール

```zsh
$ yarn add -D  husky@next
```

#### 9.2.2 Git hooks の有効化

```
$ yarn husky install
```

#### 9.2.3 フックスクリプトを追加

Git コマンド実行時に以下の処理を実行するようにします。

- コミット前にステージにあるファイルを対象に ESLint の実行
- プッシュ前にすべてのテストの実行

```
$ yarn husky add pre-commit "yarn lint-staged"
$ yarn husky add pre-push "yarn test"
```

#### 10. 環境変数の追加

```
$ touch .env.development
$ touch .env.production
```

Document コンポーネントのサイト URL に環境変数を設定します。

`_document.tsx`

```js
class MyDocument extends Document implements MyDocumentInterface {
  // 環境変数を追加
  url = process.env.NEXT_PUBLIC_SITE_URL
}
```
