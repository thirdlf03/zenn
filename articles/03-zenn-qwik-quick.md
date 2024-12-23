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
公式チュートリアルをやっていく
https://qwik.dev/docs/getting-started/


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

サーバーからクライアントへ

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
            <div>
                {zennArticles.value.articles.map((article: any) => (  
                    <div key={article.id} align="center">  
                        <p>記事名: {article.title}</p>  
                        <p>いいね数: {article.liked_count}</p>  
                    </div>
                ))}  
            </div>  
        </div>
    )  
})

```

![[03-zenn-api.png]]

ふむ

## Form使ってみる

クライアントからサーバーへ

routeActions$を使う

```tsx
import { component$ } from '@builder.io/qwik'  
import { routeLoader$, Form, routeAction$ } from '@builder.io/qwik-city';  
  
export const useZennArticle = routeLoader$(async () => {  
    const response = await fetch("https://zenn.dev/api/articles?username=thirdlf&order=latest");  
    return response.json();  
})  
  
export const useVoteAction = routeAction$((props) => {  
    console.log('VOTE', props);  
});  
  
export default component$(() => {  
    const zennArticles = useZennArticle();  
    const voteAction = useVoteAction();  
    return (  
        <div>  
            <h1>Hello, World!</h1>  
            <div>                {zennArticles.value.articles.map((article: any) => (  
                    <div key={article.id} align="center">  
                        <p>記事名: {article.title}</p>  
                        <p>いいね数: {article.liked_count}</p>  
                        <Form action={voteAction}>  
                            <input type="hidden" name="voteID" value={article.id} />  
                            <button name="vote" value="up">👍</button>  
                            <button name="vote" value="down">👎</button>  
                        </Form>                    </div>                ))}  
            </div>  
        </div>    )  
})
```

![[03-vote.png]]
## 状態管理
reactでいう、useStateかな
useSignalを使う

```tsx
import { component$, useSignal } from '@builder.io/qwik'  
import { routeLoader$, Form, routeAction$ } from '@builder.io/qwik-city';  
  
export const useZennArticle = routeLoader$(async () => {  
    const response = await fetch("https://zenn.dev/api/articles?username=thirdlf&order=latest");  
    return response.json();  
})  
  
export const useVoteAction = routeAction$((props) => {  
    console.log('VOTE', props);  
});  
  
export default component$(() => {  
    const isLike = useSignal(false);  
    const zennArticles = useZennArticle();  
    const voteAction = useVoteAction();  
    return (  
        <div>  
            <h1>Hello, World!</h1>  
            <div>                {zennArticles.value.articles.map((article: any) => (  
                    <div key={article.id} align="center">  
                        <p>記事名: {article.title}</p>  
                        <button onClick$={() => {  
                            article.liked_count > 0 ? isLike.value = !isLike.value : isLike.value = false;  
                        }}>いいねチェック  
                        </button>  
                        {isLike.value ? <p>いいね数: {article.liked_count}</p> : <p>いいね数チェックしてね</p>}  
                        <Form action={voteAction}>  
                            <input type="hidden" name="voteID" value={article.id} />  
                            <button name="vote" value="up">👍</button>  
                            <button name="vote" value="down">👎</button>  
                        </Form>                    </div>                ))}  
            </div>  
        </div>    )  
})

```

### 状態の監視
reactでいう、useEffect的なやつが
useTask$
これは、clientでもserverでもどっちの場合でも実行される
useTask$` is executed on both the server and the client

```tsx
import { component$, useSignal, useTask$ } from '@builder.io/qwik'  
import { routeLoader$, Form, routeAction$, server$ } from '@builder.io/qwik-city';  
  
export const useZennArticle = routeLoader$(async () => {  
    const response = await fetch("https://zenn.dev/api/articles?username=thirdlf&order=latest");  
    return response.json();  
})  
  
export const useVoteAction = routeAction$((props) => {  
    console.log('VOTE', props);  
});  
  
export default component$(() => {  
    const isLike = useSignal(false);  
    const zennArticles = useZennArticle();  
    const voteAction = useVoteAction();  
  
    useTask$(({ track }) => {  
        track(() => isLike.value);  
        console.log('(isomorphic)', isLike.value);  
        server$(() => {  
            console.log('(server)', isLike.value);  
        })();  
    });  
  
    return (  
        <div>  
            <h1>Hello, World!</h1>  
            <div>                {zennArticles.value.articles.map((article: any) => (  
                    <div key={article.id} align="center">  
                        <p>記事名: {article.title}</p>  
                        <button onClick$={() => {  
                            article.liked_count > 0 ? isLike.value = !isLike.value : isLike.value = false;  
                        }}>いいねチェック  
                        </button>  
                        {isLike.value ? <p>いいね数: {article.liked_count}</p> : <p>いいね数チェックしてね</p>}  
                        <Form action={voteAction}>  
                            <input type="hidden" name="voteID" value={article.id} />  
                            <button name="vote" value="up">👍</button>  
                            <button name="vote" value="down">👎</button>  
                        </Form>                    </div>                ))}  
            </div>  
        </div>    )  
})
```
![[03-state.png]]

## Styling
helloディレクトリにcssファイルを追加する
```bash
touch src/routes/hello/index.css
```

index.css
```css
p {
  font-weight: bold;
  color: red;
}
```

cssを読み込む
index.tsx
```tsx
import { component$, useSignal, useTask$, useStylesScoped$ } from '@builder.io/qwik'  
import { routeLoader$, Form, routeAction$, server$ } from '@builder.io/qwik-city';  
import styles from "./index.css?inline";  
  
export const useZennArticle = routeLoader$(async () => {  
    const response = await fetch("https://zenn.dev/api/articles?username=thirdlf&order=latest");  
    return response.json();  
})  
  
export const useVoteAction = routeAction$((props) => {  
    console.log('VOTE', props);  
});  
  
export default component$(() => {  
    useStylesScoped$(styles);  
    const isLike = useSignal(false);  
    const zennArticles = useZennArticle();  
    const voteAction = useVoteAction();  
  
    useTask$(({ track }) => {  
        track(() => isLike.value);  
        console.log('(isomorphic)', isLike.value);  
        server$(() => {  
            console.log('(server)', isLike.value);  
        })();  
    });  
  
    return (  
        <div>  
            <h1>Hello, World!</h1>  
            <div>                {zennArticles.value.articles.map((article: any) => (  
                    <div key={article.id} align="center">  
                        <p>記事名: {article.title}</p>  
                        <button onClick$={() => {  
                            article.liked_count > 0 ? isLike.value = !isLike.value : isLike.value = false;  
                        }}>いいねチェック  
                        </button>  
                        {isLike.value ? <p>いいね数: {article.liked_count}</p> : <p>いいね数チェックしてね</p>}  
                        <Form action={voteAction}>  
                            <input type="hidden" name="voteID" value={article.id} />  
                            <button name="vote" value="up">👍</button>  
                            <button name="vote" value="down">👎</button>  
                        </Form>                    </div>                ))}  
            </div>  
        </div>    )  
})
```

![[03-style.png]]


## preview
previewなるものがあるらしい
本番ビルドで試せる
```bash
npm run preview
```

### 比較用
dev
``` bash
npm start
```

![[03-npm-start.png]]

production
```bash
npm run preview
```

![[03-preview.png]]

若干パフォーマンス良くなってる
## まとめ

### コンポーネント作成

**`component$`** を使って、Qwikコンポーネントを定義する。

###  データ取得

**`routeLoader$`** を使って、データを取得する。 サーバーサイドで実行され、必要なデータを取得してからページを表示。

### アクション

**`routeAction$`** を使って、ユーザーのインタラクションなどのイベントをトリガー。フォームの送信やボタンのクリックなど、状態変更を伴う操作で使用する。

### 状態管理

**`useSignal`** を使って、状態管理。

### タスク実行と追跡

**`useTask$`** を使って、副作用や状態の追跡を実装。状態の変化やデータの同期処理に利用する。

### CSSの反映

**`useStylesScoped$`** を使って、コンポーネント単位でスコープ化されたCSSを適用。

## 完走した感想
書いてる感じはほぼreact


