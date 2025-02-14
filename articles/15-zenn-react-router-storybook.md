---
published: false
cssclasses:
  - zenn
type: tech
emoji: 📕
title: react-router v7 + storybook + lint周りの環境整備
topics: 
date: 2025-02-14
AutoNoteMover: disable
url: https://zenn.dev/thirdlf/articles/15-zenn-react-router-storybook
tags: []
aliases:
---
```bash
npm create vite@latest
```
![](スクリーンショット%202025-02-14%2019.18.18.png)

```bash
npx storybook init
```


この状態だとエラー吐きます。
![](スクリーンショット%202025-02-14%2019.20.46.png)

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
```git
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
+      builder: {  
+        viteConfigPath:
+         'vite.story.config.ts',  
+      }  
    },  
  },  
};  
export default config;
```

これでstorybookを起動してみる
```
npm run storybook
```

起動してればok

次に、muiのインストールをする
```bash
npm install @mui/material @emotion/react @emotion/styled @mui/icons-material
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
```git
+import type { Preview, ReactRenderer } from +"@storybook/react";  
+import { CssBaseline, ThemeProvider } from +"@mui/material";  
+import { withThemeFromJSXProvider } from +"@storybook/addon-themes";  
+import { lightTheme, darkTheme } from +"~/components/theme";  
  
const preview: Preview = {  
  parameters: {  
    controls: {  
      matchers: {  
        color: /(background|color)$/i,  
        date: /Date$/i,  
      },  
    },  
  },  
+  decorators: [  
+    withThemeFromJSXProvider<ReactRenderer>({ 
+      themes: {  
+        light: lightTheme,  
+        dark: darkTheme,  
+      },  
+      defaultTheme: "dark",  
+      Provider: ThemeProvider,  
+      GlobalStyles: CssBaseline,  
+    }),  
+  ],  
};  
  
export default preview;
```

これでstorybookを確認するとlight themeとdark theme切り替えられるようになっている。
