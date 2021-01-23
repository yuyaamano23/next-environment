# 環境構築

とりあえずこれ見てやった。<br>
[2020 年師走における Next.js をベースとしたフロントエンドの環境構築](https://zenn.dev/higa/articles/d7bf3460dafb1734ef43)

<br>
<br>
<br>

## 1. プロジェクトの作成

**ルートとなるプロジェクトの作成**<br>

```
$ mkdir next-project
```

**Next.js のプロジェクトをセットアップ**<br>
oNext.js で v9.1 から src に pages などを入れることができるらしいンゴ。のちのち複雑になる可能性を考えて src ディレクトリへ移動しておくことにする。

```
$ yarn create next-app .
```

**ディレクトリを移動**

```
$ mkdir src
$ mv pages/ src/pages
$ styles/ src/styles
```

<br>

## 2. TypeScript の導入

**設定ファイルのセットアップ**<br>
これで tsconfig.json の中身と next-env.d.ts が新規作成される

```
$ touch tsconfig.json
$ yarn add --dev typescript @types/react @types/node
$ yarn dev
```

**TS ファイルに変換**<br>

```
$ find src/pages -name "_app.js" -or -name "index.js" | sed 'p;s/.js$/.tsx/' | xargs -n2 mv
$ find src/pages/api -name "*.js" | sed 'p;s/.js$/.ts/' | xargs -n2 mv
```

**App コンポーネントを TypeScript に対応させる**<br>
[変更後の\_app.tsx](https://github.com/yuyaamano23/next-typescript-todo/commit/f7b9027ae2fa4f2d6c9b7f4343d2c60884a5220a)

**page コンポーネントを TypeScript に対応させる**<br>
[変更後の index.tsx](https://github.com/yuyaamano23/next-typescript-todo/blob/d468c45c23622269f939bbc0ebb02897ff5252e8/src/pages/index.tsx)

**API フォルダの削除**<br>
フロンドエンドで API を作成する予定はないので削除!!!(デスノート:みかみ)

<br>

## 3. Document コンポーネントを追加

Document コンポーネント を使うと、初期状態だと自動で追加される html タグ, や body タグに変更を加えることができます。

```
$ touch src/pages/_document.jsx
```

以下参照<br>
↓<br>
[追加した Document.tsx](https://github.com/yuyaamano23/next-typescript-todo/commit/0922e33cd3cc72e72e13ddf505eae3524b188838)

<br>

## 4. ベース URL を設定

src に pages ディレクトリなどを移動しましたので、src をベース URL に設定するよー。

**TypeScript の設定を変更**<br>
TypeScript の設定にモジュールインポートのベース URL を追記。<br>
_tsconfig.json_ ↓

```
{
  "compilerOptions": {
    // ベース URL を追加
    "baseUrl": "src"
  }
}
```

各コンポーネントのモジュールインポートの指定を、ベース URL 指定に変更するよー。

```
$ sed -i '' -e 's/..\/styles/styles/' src/pages/_app.tsx
$ sed -i '' -e 's/..\/styles/styles/' src/pages/index.tsx
```

<br>

## 5. スタイルの設定

Next.js はデフォルトで CSS Modules に対応している。今回は取りあえす CSS Modules を採用する。<br>
揺れ動く感情の中 CSS Module の採用を後押ししてくれた記事 ↓<br>
[styled-components（CSS in JS）をやめた理由と、不完全な CSS Modules を愛する方法](https://qiita.com/jagaapple/items/7f74fc32c69f5b731159)

**SASS のインストール**<br>

```
$ yarn add -D sass
```

**SASS ファイルに変換**<br>
`src/styles`ディレクトリ内の CSS ファイルを SASS ファイルに変換するよー。

```
$ find src/styles -name "*.css" | sed 'p;s/.css$/.scss/' | xargs -n2 mv
```

**SASS ファイルを読み込むように変更**<br>

```
$ sed -i '' -e 's/\.css/\.scss/' src/pages/_app.tsx
$ sed -i '' -e 's/\.css/\.scss/' src/pages/index.tsx
```

<br>

**styles ディレクトリ内の構造を工夫する**<br>
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
