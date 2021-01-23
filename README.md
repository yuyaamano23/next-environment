# 環境構築

とりあえずこれ見てやった。<br>
[2020 年師走における Next.js をベースとしたフロントエンドの環境構築](https://zenn.dev/higa/articles/d7bf3460dafb1734ef43)

<br>
<br>
<br>

**ルートとなるプロジェクトの作成**<br>

```
$ mkdir next-project
```

**Next.js のプロジェクトをセットアップ**<br>

```
$ yarn create next-app .
```

**ディレクトリを移動**

```
$ mkdir src
$ mv pages/ src/pages
$ styles/ src/styles
```

**TypeScript の導入**<br>
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
