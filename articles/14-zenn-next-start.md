---
published: false
cssclasses:
  - zenn
type: tech
emoji: 📕
title: t４エスt
topics: 
date: 2025-02-13
AutoNoteMover: disable
url: https://zenn.dev/thirdlf/articles/14-zenn-next-start
tags: []
aliases:
---
手順

bunでやります
![](/images/article-14/init.png)
```bash
bunx create-next-app@latest
```

https://mui.com/material-ui/integrations/nextjs/
```
bun install @mui/material @emotion/react @emotion/styled @mui/material-nextjs @emotion/cache
```


bunx storybook@latest init


```
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

```
import type { Preview, ReactRenderer } from "@storybook/react";  
  
import { CssBaseline, ThemeProvider } from "@mui/material";  
import { withThemeFromJSXProvider } from "@storybook/addon-themes";  
import { lightTheme, darkTheme } from "../src/components/theme/theme";  
  
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
  ]  
};  
  
export default preview;
```

```
import React from 'react';  
import {  
    Button as MuiButton,  
    ButtonProps as MuiButtonProps,  
} from '@mui/material';  
  
type ButtonBaseProps = Pick<MuiButtonProps, 'variant' | 'size' | 'color'>;  
  
export interface ButtonProps extends ButtonBaseProps {  
    label: string;  
}  
export const Button = ({ label, ...rest }: ButtonProps) => (  
    <MuiButton {...rest}>{label}</MuiButton>  
);
```

```
import { Button } from './button.component';  
  
export default {  
    title: 'Design System/Button',  
    component: Button,  
    argTypes: {  
        variant: {  
            options: ['contained', 'outlined', 'text'],  
            control: { type: 'radio' },  
        },  
        color: {  
            options: ['primary', 'secondary', 'error', 'info'],  
            control: { type: 'radio' },  
        },  
    },  
};  
  
export const Default = {  
    args: {  
        label: 'button',  
    },  
};
```


Chromaticデプロイ
```
bun install --dev chromatic
```

```
bunx chromatic --project-token=******
```
もう一回

```
bunx chromatic --project-token=chpt_2dda96e3d8e77a1
```
