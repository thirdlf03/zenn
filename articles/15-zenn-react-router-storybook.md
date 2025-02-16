---
published: true
cssclasses:
  - zenn
type: tech
emoji: 📕
title: React + Storybook で作る、保守性を意識したフロントエンド開発環境
topics: 
date: 2025-02-14
AutoNoteMover: disable
url: https://zenn.dev/thirdlf/articles/15-zenn-react-router-storybook
tags:
  - react
  - storybook
  - 開発環境
aliases:
---
# はじめに
よくハッカソンに出ているため、今まで使い捨てのようなプログラムを書くことが多く、メンテナンスについてあまり意識していませんでした。
しかし、今回作ろうとしているのが長期的にメンテナンスが必要になってくるものかつ複数人が開発に携わるものになっているので、保守性やdevopsを意識して開発環境を作っていきました。


今回やっていくのは主にフロント(react)です。

# 開発環境の紹介
### React (React Router v7)
現在、うちの団体で初心者にフロントを教えている時にReactを採用していることもありその人たちが開発に携われるようにReactにしました。

今だとNextが流行ってるんですが、学習コストの面やReact Routerも良さげだったので採用しています。

Vue + vuetifyの組み合わせも検討しましたが、後述するStorybookとの相性があまり良くなさそうだっため今回は見送りです。

### Storybook
今回の主役。
前々から名前だけ知っているみたいな状態だったんですが、いざ使ってみるとこれは便利だなと思い採用しました。
Storybookは簡単に説明するとUIカタログなんですが、VRT( Visual Regression Test )ができたり、自動でドキュメント作ってくれたりしてくれます。
コンポーネントの管理ってめんどくさいイメージがあったので、それを解決してくれそうです。
現代のフロントエンド開発で必須になってきてると言う話があるのもうなづけます。

例 ）
Amebaのデザインシステムである「spindle」のstorybookです。
[https://ameba-spindle.web.app/](https://ameba-spindle.web.app/ "https://ameba-spindle.web.app/")

https://storybook.js.org/

### Chromatic
Storybookを使う上では欠かせないものです。
Storybookをチームで共有したり、UIレビューしたりする場合に役に立ちます。

無料プランがあるので、ぜひ試してみてください！

https://www.chromatic.com/

### Playwright
E2Eテストを自動化できるものです。

### Storybook test runner
Storybook test runnerは、JestとPlaywrightで動くtest runnerです。

### ESlint + Prettier
ここら辺は定番ですね。
ESlintはjs/tsなどの静的解析ツールで、コードの品質を高めてくれます。Prettierは自動でコードを整形してくれるものです。
チーム開発する際に、あるのとないのでは開発効率が違うので入れておきます。

### husky + lint-staged
こっちは定番ほどではないですが、入れておくとそこそこ恩恵があります。
husky + lint-stagedこの二つを使うことで、コミット前にコード整形したり、lint走らせてくれたりします。
ただ、前使用した時に動作が不安定だったのが懸念が残るところです。

### GitHub Actions
ci/cdパイプライン組むのに使用。便利

### Dependabot
リポジトリ内の依存関係をチェックしてくれるbot。今回、長期にわたってメンテナンスしていくことを見据えているので、導入してみました。

# 環境構築
今回、パッケージマネージャーはnpmを使用しています。
使っているnodeのversionは、v23.6.1です。

参考にされていただいた記事
 - https://zenn.dev/akkey247/articles/20240417_remix_environment_construction_2
 - https://zenn.dev/mobdev/articles/75de17313f3829

パッケージ
- react
- react-router v7
- storybook
- playwright
- storybook test runner
- Chromatic
- mui
- ESlint
- eslint-plugin-react
- Prettier
- eslint-config-prettier
- husky
- lint-staged


## React Router
```bash
npm create vite@latest
```
![](/images/article-15/react.png)

## Storybook
```bash
npx storybook init
```


この状態だとエラー吐きます。
![](/images/article-15/react-story.png)

storybook用のconfigファイルを作る
```bash
touch vite.story.config.ts
vim vite.story.config.ts
```

vite.story.config.ts
```ts
import { defineConfig, loadEnv } from 'vite';  
import tsconfigPaths from 'vite-tsconfig-paths';  
  
export default defineConfig(({ mode }) => {  
  const env = loadEnv(mode, process.cwd(), '');  
  process.env = { ...process.env, ...env };  
  return {  
    plugins: [tsconfigPaths()],  
  };  
});
```

storybookのmain.tsをいじる

.storybook/main.ts
```ts
import type { StorybookConfig } from "@storybook/react-vite";  
  
const config: StorybookConfig = {  
  stories: [  
    "../stories/**/*.mdx",  
    "../stories/**/*.stories.@(js|jsx|mjs|ts|tsx)",  
  ],  
  addons: [  
    "@storybook/addon-onboarding",  
    "@storybook/addon-essentials",  
    "@chromatic-com/storybook",  
    "@storybook/addon-interactions", 
  ],  
  framework: {  
    name: "@storybook/react-vite",  
    options: {  
      builder: {  
        viteConfigPath:
         'vite.story.config.ts',  
      }  
    },  
  },  
};  
export default config;
```

これでstorybookを起動してみる
```bash
npm run storybook
```

起動してればok

### playwright
```bash
npx playwright install
```
### Storybook test runner
```bash
npm install @storybook/test-runner --save-dev
```
scriptsに追加

package.json
```
"test-storybook": "test-storybook",
```

動作確認する
```bash
npm run storybook
npm run test-storybook
```


## MUI
次に、muiのインストールをする
```bash
npm install @mui/material @emotion/react @emotion/styled
```

muiのテーマを使えるように準備する
```bash
mkdir app/components
touch app/components/theme.tsx
vim app/components/theme.tsx
```

theme.tsx
```ts
import { createTheme } from "@mui/material";  
  
export const lightTheme = createTheme({  
  palette: {  
    mode: "light",  
  },  
});  
  
export const darkTheme = createTheme({  
  palette: {  
    mode: "dark",  
  },  
});
```
storybookで読み込むようにする

.storybook/preview.ts
```ts
import type { Preview, ReactRenderer } from "@storybook/react";  
import { CssBaseline, ThemeProvider } from "@mui/material";  
import { withThemeFromJSXProvider } from "@storybook/addon-themes";  
import { lightTheme, darkTheme } from "~/components/theme";  
  
const preview: Preview = {  
  parameters: {  
    controls: {  
      matchers: {  
        color: /(background|color)$/i,  
        date: /Date$/i,  
      },  
    },  
  },  
  decorators: [  
    withThemeFromJSXProvider<ReactRenderer>({ 
      themes: {  
        light: lightTheme,  
        dark: darkTheme,  
      },  
      defaultTheme: "dark",  
      Provider: ThemeProvider,  
      GlobalStyles: CssBaseline,  
    }),  
  ],  
};  
  
export default preview;
```

これでstorybookを確認するとlight themeとdark theme切り替えられるようになっている。

eslintとprettier

eslint
```bash
npm init @eslint/config@latest
```

![](/images/article-15/eslint.png)

## prettier
https://prettier.io/docs/install

```bash
npm install --save-dev prettier
node --eval "fs.writeFileSync('.prettierrc','{}\n')"
node --eval "fs.writeFileSync('.prettierignore','# Ignore artifacts:\nbuild\ncoverage\n')"
```

## eslint-config-prettierとeslint-plugin-react
```bash
npm install --save-dev eslint-config-prettier eslint-plugin-react
```

eslint.config.js
```js
import globals from "globals";  
import pluginJs from "@eslint/js";  
import tseslint from "typescript-eslint";  
import react from "eslint-plugin-react";  
import eslintConfigPrettier from "eslint-config-prettier";  
  
  
/** @type {import('eslint').Linter.Config[]} */  
export default [  
  {files: ["**/*.{js,mjs,cjs,ts,jsx,tsx}"]},  
  {plugins: { react }},  
  {languageOptions: { globals: globals.browser }},  
  pluginJs.configs.recommended,  
  ...tseslint.configs.recommended,  
  eslintConfigPrettier,  
];
```

この後lintで弾かれたため、一旦無視するようにしてます
home.tsx
```ts
import type { Route } from "./+types/home";  
import { Welcome } from "../welcome/welcome";  
import React from "react";  
  
// eslint-disable-next-line no-empty-pattern  
export function meta({}: Route.MetaArgs) {  
  return [  
    { title: "New React Router App" },  
    { name: "description", content: "Welcome to React Router!" },  
  ];  
}  
  
export default function Home() {  
  return <Welcome />;  
}
```

## husky + lint-staged
```bash
npm install --save-dev husky lint-staged
npx husky init
node --eval "fs.writeFileSync('.husky/pre-commit','npx lint-staged\n')"
```

package.json
```json
{  
  "name": "objectt",  
  "private": true,  
  "type": "module",  
  "scripts": {  
    "build": "react-router build",  
    "dev": "react-router dev",  
    "start": "react-router-serve ./build/server/index.js",  
    "typecheck": "react-router typegen && tsc",  
    "storybook": "storybook dev -p 6006",  
    "build-storybook": "storybook build",  
    "prepare": "husky"  
  },  
  "lint-staged": {  
    "**/*.{js,mjs,cjs,ts,jsx,tsx}": [  
        "prettier --write --ignore-unknown",  
        "eslint --fix"  
    ]  
  },  
  "dependencies": {  
    "@emotion/react": "^11.14.0",  
    "@emotion/styled": "^11.14.0",  
    "@fontsource/roboto": "^5.1.1",  
    "@mui/icons-material": "^6.4.4",  
    "@mui/material": "^6.4.4",  
    "@react-router/node": "^7.1.5",  
    "@react-router/serve": "^7.1.5",  
    "isbot": "^5.1.17",  
    "react": "^19.0.0",  
    "react-dom": "^19.0.0",  
    "react-router": "^7.1.5"  
  },  
  "devDependencies": {  
    "@chromatic-com/storybook": "^3.2.4",  
    "@eslint/js": "^9.20.0",  
    "@react-router/dev": "^7.1.5",  
    "@storybook/addon-essentials": "^8.5.5",  
    "@storybook/addon-interactions": "^8.5.5",  
    "@storybook/addon-onboarding": "^8.5.5",  
    "@storybook/addon-themes": "^8.5.5",  
    "@storybook/blocks": "^8.5.5",  
    "@storybook/react": "^8.5.5",  
    "@storybook/react-vite": "^8.5.5",  
    "@storybook/test": "^8.5.5",  
    "@tailwindcss/vite": "^4.0.0",  
    "@types/node": "^20",  
    "@types/react": "^19.0.1",  
    "@types/react-dom": "^19.0.1",  
    "eslint": "^9.20.1",  
    "eslint-config-prettier": "^10.0.1",  
    "eslint-plugin-react": "^7.37.4",  
    "globals": "^15.15.0",  
    "husky": "^9.1.7",  
    "lint-staged": "^15.4.3",  
    "prettier": "^3.5.1",  
    "react-router-devtools": "^1.1.0",  
    "storybook": "^8.5.5",  
    "tailwindcss": "^4.0.0",  
    "typescript": "^5.7.2",  
    "typescript-eslint": "^8.24.0",  
    "vite": "^5.4.11",  
    "vite-tsconfig-paths": "^5.1.4"  
  }  
}
```

## chromatic

```
npm install --dev chromatic
```

chromaticにログインして新しくプロジェクト用意する
https://www.chromatic.com/

GitHubから繋げる
![](/images/article-15/storybook.png)

storybookを選ぶ
![](/images/article-15/chromatic.png)

project-tokenが表示されるので繋げる。
```bash
npx chromatic --project-token=~~~~
```

チームで開発する場合、chromaticへの招待を忘れずに

# GitHub Actions
デプロイ先は決まってないので、ciだけ用意していきます。

ci.yml
```yaml
name: "CI"  
  
on:  
  workflow_dispatch:  
  pull_request:  
    types: [opened]  
    branches:  
      - develop  
  
jobs:  
  setup:  
    name: Setup and Cache  
    runs-on: ubuntu-latest  
    outputs:  
      cache-hit: ${{ steps.cache.outputs.cache-hit }}  
    steps:  
      - name: Checkout code  
        uses: actions/checkout@v4  
        with:  
          fetch-depth: 0  
      - uses: actions/setup-node@v4  
        with:  
          node-version: 22.12.0  
      - uses: actions/cache@v4  
        id: cache  
        with:  
          path: node_modules  
          key: ${{ runner.arch }}-${{ runner.os }}-node-${{ steps.setup_node.outputs.node-version }}-npm-${{ hashFiles('**/package-lock.json') }}  
      - name: Install dependencies  
        if: steps.cache.outputs.cache-hit != 'true'  
        run: npm ci  
  
  chromatic:  
    name: Run Chromatic  
    runs-on: ubuntu-latest  
    needs: setup  
    steps:  
      - name: Checkout code  
        uses: actions/checkout@v4  
        with:  
          fetch-depth: 0  
      - uses: actions/setup-node@v4  
        with:  
          node-version: 22.12.0  
      - name: Restore node_modules from cache  
        uses: actions/cache@v4  
        with:  
          path: node_modules  
          key: ${{ runner.arch }}-${{ runner.os }}-node-${{ steps.setup_node.outputs.node-version }}-npm-${{ hashFiles('**/package-lock.json') }}  
      - name: Run Chromatic  
        uses: chromaui/action@latest  
        with:  
          projectToken: ${{ secrets.CHROMATIC_PROJECT_TOKEN }}  
  
  storybook-test:  
    name: Run Storybook Tests  
    timeout-minutes: 60  
    runs-on: ubuntu-latest  
    needs: setup  
    steps:  
      - name: Checkout code  
        uses: actions/checkout@v4  
        with:  
          fetch-depth: 0  
      - uses: actions/setup-node@v4  
        with:  
          node-version: 22.12.0  
      - name: Restore node_modules from cache  
        uses: actions/cache@v4  
        with:  
          path: node_modules  
          key: ${{ runner.arch }}-${{ runner.os }}-node-${{ steps.setup_node.outputs.node-version }}-npm-${{ hashFiles('**/package-lock.json') }}  
      - name: Install Playwright  
        run: npx playwright install --with-deps  
      - name: Run Storybook Tests  
        run: npm run build-storybook --quiet  
      - name: Serve Storybook and run tests  
        run: |  
          npx concurrently -k -s first -n "SB,TEST" -c "magenta,blue" \  
            "npx http-server storybook-static --port 6006 --silent" \  
            "npx wait-on tcp:127.0.0.1:6006 && npm run test-storybook"
```

# Dependabot
.github/dependabot.yml
```yaml
version: 2  
updates:  
  - package-ecosystem: "npm"  
    directory: "/"  
    schedule:  
      interval: "weekly"
```

実行時間
![](/images/article-15/actions.png)

最適化していきたい

# 最後に
今の自分で考えうる保守性の高い構成にしてみました。
ただ、実際に開発してみてここ修正して方がいいなみたいなことがあればこの記事も修正しようと思います。
