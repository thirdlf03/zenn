---
published: false
cssclasses:
  - zenn
type: tech
emoji: 🚀
title: Qwikを使い爆速でつよつよなポートフォリオサイト作る
topics: 
date: 2024-12-24
AutoNoteMover: disable
url: https://zenn.dev/thirdlf/articles/05-zenn-qwik-portfolio
tags:
  - "#type/zenn"
  - Qwik
aliases:
---
# 概要
今までやってきたことを書くポートフォリオサイト作りたいなーって思っていました。
ただ、普通に作っても面白くないしとにかく早くて快適なサイトを作りたいなー
って時にQwikの存在を知り、これはやるしかないって事で作りました。
これからポートフォリオサイト作る人の参考になれば嬉しいです !

Qwik初学者なので、間違っている箇所があれば遠慮なく指摘してください

作ったやつ
https://pages.thirdlf03.com/

githubのリポ
https://github.com/thirdlf03/portfolio

# 設計
## 構成
ポートフォリオサイト作るってなった時に、
- GitHubのリポジトリ
- Zennの記事
- 作った作品
- 連絡フォーム
が欲しいな思っていました。

### GitHub API
ただ、ネックなのがGitHubのリポジトリ取ってくるためにGitHub APIを叩く必要があり、うーん
って感じでした。
しかし、
https://github.com/unjs/ungh
なんと、お手軽に叩けちゃうものがあり今回はこれを使わせてもらいました。

example )
https://ungh.cc/users/thirdlf03/repos

### Zenn API
一応、zennのapiは存在しているようなので使わせてもらいました。~~怒られたら修正します~~

example )
https://zenn.dev/api/articles?username=thirdlf&order=latest

## デザイン
デザインを考えるのが苦手なので、いつも苦労してます ( ；∀；)
最近は、v0やclaudeなんかにある程度デザインを考えさせて、それを自分で改変してます。

# 技術スタック
## フロントエンド
もちろん、Qwik⭐️⭐️
あとは、タイピングアニメーション用にtyped.js
Tailwind使うか迷いましたが、今回は生のCSSで書きました。ツラカッタ

## バックエンド
今回、お手軽に済ませたかったのでSupabase使いました

## デプロイ
最近、Cloudflareにドメイン移管したのでCloudflare Pagesでホスティングしてみた

```bash
npm run qwik add
```
で Adapter: Cloudflare Pagesを導入

あとはCloudflare側で設定するだけ。超簡単！

# パフォーマンス
何を持ってつよつよとするのかって話なんですが、lighthouseの結果を持ってつよつよかどうか
判断します。

wkwk

![](/images/article-5/05-performance.png)

なかなかいいのでは！？

# 工夫したところ
## Qwikらしい書き方をした
lazy loadingを意識して、書いていくことによってパフォーマンスが上がっている。はず..

特に、でかくなりそうなコンポーネントは分割して読み込みしています。
```tsx
export const Zenn = component$(() => {  
    useStylesScoped$(styles);  
    const zennArticles = useZennAPI()  
    return (  
        <>  
            <h1 class="section-text">Zenn Articles</h1>  
                <div class="container" style={{marginBottom: "2rem"}}>  
                {zennArticles.value.articles.map((article: any) => (  
                    <div key={article.id} class="container-content">  
                        <h1 class="zenn-title"><a href={`https://zenn.dev/${article.path}`} target="_">{article.title}</a></h1>  
                    </div>                
                ))}  
            </div>  
        </>    
    )  
})  
  
export default component$(() => {  
    useStylesScoped$(styles);  
    return (  
        <div class="main-container">  
            <Profile />
            <Skill/>
            <Github/> 
            <Zenn/>        
        </div>
    );  
});

```


## 生のCSSでスタイリングした
開発のしやすさで言うと、tailwindやCSS-in-JS使うと楽だったが少しでもパフォーマンスが良くなるように生のCSSで書きました。


## お問い合わせ通知
discordのwebhookを使い、お問い合わせが送られるとdiscordに通知が飛んでくるようにしました。
![](/images/article-5/05-discord.png)


# 辛かったところ
## SSRに慣れていなかった
NextやNuxtを触ってきてない関係上、SSRってものに慣れていなくてdocumentを扱えず、
document is not define　エラーに悩まされていました。

解決策として、useTask$ ではなく

before
```tsx
useTask$(() => {  
    const typed = new Typed('#typed-output', options);  
  
    return () => {  
        typed.destroy();  
    };  
});

```
useVisibleTask$を使うことで解決しました。

after
```tsx
useVisibleTask$(() => {  
    const typed = new Typed('#typed-output', options);  
  
    return () => {  
        typed.destroy();  
    };  
}, {  
    strategy: "intersection-observer",  
});

```

##  ReactっぽくてReactじゃないところ
文法的にはReactっぽいんですが、所々Reactとは違う箇所があり書いていて悩みました。

例えば、ReactでいうuseEffectみたいなものがuseTask$なんですが、useTaskはデフォルトでは
初回レンダリング時のみ動作し、値を追跡する場合は

React
```tsx
const email = useState("");

useEffect(() => {
	console.log("変化のたびに動くよ!");
}, [email])
```

Qwik
```tsx
const email = useSignal<string>("");  
const isValid = useSignal<boolean>(true);  
  
useTask$(({ track }) => {  
    track(() => isValid);  
    const newText = track(email);  
    console.log("変化のたび動くよ！") ;
});

```

みたいな感じです。

# 感想
Qwikのおかげで、フロントエンドに精通していなくても自分でも高いパフォーマンスが出せるものを作れました。Qwikを極めたい。
