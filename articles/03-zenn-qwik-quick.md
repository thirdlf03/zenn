---
published: false
cssclass: zenn
type: "tech"
emoji: "🚀"

title: "QwikをQuick入門"
topics: []
date: 2024-12-23
AutoNoteMover: disable
url: "https://zenn.dev/thirdlf/articles/03"
tags: [" #type/zenn "]
aliases: 
---
# 概要
QwikをQuick入門したい。


## セットアップ
今回は、Playground Appで作成
```bash
npm create qwik@latest
cd qwik-app
npm start
```

## Hello World
```bash
mkdir src/routes/hello
touch src/routes/hello/index.tsx
```

index.tsx
```tsx
import { component$ } from '@builder.io/qwik'  
  
export default component$(() => {  
  return (  
      <h1>Hello, World!</h1>  
  )  
})

```

これで、http://localhost:5173/hello/
にアクセスするとHello Worldできる。

![[03-hello.png]]


## WebAPIからデータ取得
今回は、zennのapi使用
 [https://zenn.dev/api/articles?username=thirdlf&order=latest](https://zenn.dev/api/articles?username=thirdlf&order=latest "https://zenn.dev/api/articles?username=thirdlf&order=latest")

routeLoaderを使うといいらしい

```tsx
import { component$ } from '@builder.io/qwik'  
import { routeLoader$ } from '@builder.io/qwik-city';  
  
export const getZennArticle = routeLoader$(async () => {  
    const response = await fetch("https://zenn.dev/api/articles?username=thirdlf&order=latest");  
    return response.json();  
})  
  
export default component$(() => {  
    const zennArticles = getZennArticle();  
    return (  
        <div>  
            <h1>Hello, World!</h1>  
            <div>                {zennArticles.value.articles.map((article: any) => (  
                    <div key={article.id} align="center">  
                        <p>記事名: {article.title}</p>  
                        <p>いいね数: {article.liked_count}</p>  
                    </div>                ))}  
            </div>  
        </div>    )  
})

```

![[03-zenn-api.png]]

ふむ

