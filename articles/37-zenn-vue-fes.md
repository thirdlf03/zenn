---
id: 37-zenn-vue-fes
aliases:
tags:
  - vue
autonotemover: disable
cssclasses:
  - zenn
date: 2025-10-25
emoji: ☘️
published: false
title: Vue Fes Japan 2025参加してきた！
type: tech
url: https://zenn.dev/thirdlf/articles/37-zenn-vue-fes
---
# はじめに
著者のVueの知識はチュートリアルに毛が生えた程度なので、間違いがあるかもしれません。
ご理解ください。
また、感想欄は講演の内容 + 自分の感想になってます。

セッション聴きながら書いているので、誤字脱字等があるかもしれないです

# 初のVue Fes
初のVueFes。

ネームカードテンション上がる
![](/images/37/name.jpeg)


## 学生支援制度
今回、学生支援制度を使って参加しました。
福岡から東京行こうと思うと、そこそこお金がかかるので交通費を出していただけるのはすごく助かりました！ さらに、チケット代まで出していただけたのもあり本当に食費以外かからなかったです。学生にはありがたい
https://note.com/naoki_haba/n/n445d7f19fcd4



10:00 - 10:10
# オープニング
## 感想
まず、カウントダウンがかっこいい。
![](/images/37/countdown.jpg)
映像なんだこれ、カッコ良すぎる。映像がおしゃれだ
BGMも良い。

kazuponさんの英語のスピーチからスタート。2019年からVue Fesをやっているみたい。
日本人以外の参加者も増えている。
参加人数としては、2018年は約450人だったのが、今年は821人も参加してる！

EvanさんのKeynoteから始まり、29個もセッション、8個のLTがあるみたい。
↑すごい

今年から初級者じゃなくて、中級者を対象としたハンズオンを開始した。
スポンサーいっぱいだ


10:10 - 10:50
# キーノート
## speaker
Vue.js、Vite クリエーター
Evan Youさん

## 感想
日本語での挨拶があって、セッション本編は英語。
Evanさん日本語が上手！

去年からVueのダウンロード数が+27.5%増えていて、Vue3が77.6%を占めている。
Align Signals、JSで最も高速なシグナル実装。
Vapor Mode、ベース、ベースコンパイラコア、ランタイムコアがある。　適切に抽象化されている。

エンドユーザー視点の挙動だとVDOMとVaporのコンパイラーは同じだが、パフォーマンスが変わってくる。

現在は、VDOM
Vapor Modeはベンチマークスコアが他のフレームワークと比べてもかなり高い

Vue Language Tools
言語ツールにおけるIDEサポート。

Viteの話

void(0)
https://voidzero.dev/

void(0)って名前自体初耳だったが、ViteとかVitestとかを作っているところだった。 だいぶお世話になってる。

web開発に詳しくない人はそもそも、なぜJSにビルドツールがいるのか？JSってブラウザ上で実行されるだけじゃないの？

歴史的に、JSはそもそもこんなに使われるのを想定したいなかったみたい。
ブラウザで配信できるのはいいけど、色々問題が起こる。
例えば、ファイル分割したい。だったり、モジュールを何千も読み込むのは現実的じゃない

babel、JS用にJSで書かれたビルドツール。
パフォーマンスが問題になってきている。
WebpackやBabel、Jestは起動時間が遅い。ビルド時間が長いと開発体験が悪い。

そこで、過去5年間で次世代ツールが登場 
JS用に書かれているが、ネイティブ言語.
zigやRustで書かれている。

ZeroではRustを選択。

JSとNativeでは速度が違う。
 ![](/images/37/native.jpg)

void(0)
Vite、Vitest、Rolldown、Oxcなど..

Vite毎週3600万ダウンロードされているらしい、えぐい

JestがVitestに置き換わっている.　Rolldownはあまりダウンロードされていなかったが、大規模企業で稼働していて、ダウンロード数が爆発的に増えた

Oxlint
ESLintよりも50~100x 早い
600以上のESLint ruleサポートしてるらしい
カスタムJSプラグインサポート

独自のプラグインを対応しないと、エコシステム全体の移行ができない問題が発生する。確かに、lint系はカスタムプラグインをよく見かける気がするので納得

Oxfmt
いまalpha版

Pretterのテスト 92%
Prettierより45x早く、BIomeよりも2~3x早い
フレキシブルな改行オプション。 

Rolldown
ESBiuldよりも早い

Vite
マイナーバージョンなのであんまり変更ない
RSCサポート 

Vitest
4.0 just release
ブラウザモード。
インテグレーションテストが実際の環境と近くなる

Rolldown-Vite
完全に互換性がある

次のバージョンのvite
dev full bundle mode
開発者サーバーの起動が早くなる。
サーバースタートが15倍、ページ再読み込みも10倍高速化できる
↑これ、大規模になればなるほど気になる問題だから解消したら助かりそう

Rust is fast But...
Rustaceanなら、Rustで書けばいいのに...って思うことが多いが実際はそうはいかない

JS開発者はJSでプラグイン書くのを好む

多くのカスタマイズプラグインをブンsネキすると、JSでの追加解析を行なっている。
最初のアイデアでは、Rust側にすでに存在するASTをJS側に渡す
リンタールールを実装する時に、全てのASTノードにアクセスする必要はないことに気づく

遅延デジタリアライザゼーション。


現状。プロトタイプだが、 Oxc Lazy、生のoxcよりも2倍以上パフォーマンスが出る。
magic string transform。


中間ステップがJSで実行されているから遅い.できるだけ多くの作業をJSからRustへ。
Rustベースのバンドらーでk￥変換を高速化する方法

Vite+
総合ツールチェーン
便利

vite+ Plugin
Vite Pluginのスーパーセット
をうまく連携
統合ツールチェーンがAgent Mode

void(0)ってすごいな。
https://voidzero.dev/


# プラチナスポンサーセッション

10:55 - 11:05
### VueはAIに弱い？そんなの都市伝説です
#### speaker:
株式会社リンクアンドモチベーション
フロントエンドエンジニア
中上 裕基さん

#### 感想
生成AI使うならReactって風潮あるが、VueがAIに対して弱いのでは？確かに。これはVue2とVue3による、書き方の違いからきてそう。

VueってAIと相性いい。Figma MCP使っているらしい。
画面丸ごとではなく、コンポーネントごとに実装。人間も画面ごとに作らないから、コンポーネントごとに実装するってのは僕も意識してるな。 

流れとしては、コンポーネント実装、ロジック実装、PR。

ロジックの実装をしてもらうにあたり、 OpenAPIを設計し、AIのAPIとして渡す。するとAPI Client、 ピニャコラーダ使っているのでCommand, Query、生成したAPIに対して単体テストを書いてもらって、FSWモックを書いてもらっている。


暗黙的なルールを後出しするのではなく、責務で区切ったレイヤーごとに明確なルールを設定する。
PRを出すとAIがレビューしてくれる。 コーディング規約や、アーキテクチャ設計に基づくレビュー、テストに基づくレビュー。
明確なレビュー観点と必要十分な情報を与える。
簡単に言ってるけど、大変じゃない？

暗黙的にやっていることを言語化するのはかなり大変。けど情熱や好きで乗り切った。こだわり。好きって木のもちが

好きを諦めなくていい

11:05 - 11:15
### Storybook 駆動開発で実現する持続可能な Vue コンポーネント設計
#### speaker
ユニークビジョン株式会社
エンジニア
矢光 隆太郎さん

#### 感想
Storybook駆動開発とは何か。フロントエンド開発での課題があって、
- 手戻りと認識のずれ
- 品質の後回し
- アドホックな作り変え (設計せずに場当たり的に開発してコードの質が悪化)

Storybook駆動開発としては、アプローチとしてStoryを書いてから実装する。
そうすることで、コンポーネントの見た目とコンポーネントの振る舞いを明確化。

Storybook駆動のワークフロー

設計フェーズ
- コンポーネント一覧を設計
- コンポーザブル一覧を設計
- Storyを作成
実装フェーズ
- Storyに沿って実装

Propsファーストの設計が促すもの
- 関心の分離
- 処理された設計
- 問題の早期発見

知見
知見を聞けるのがありがたい。

新規開発ケーススタディ
フローは分かりやすかったが、Story作成の知見があまりないので、モック作ったり、ユーティリティ関数、お手本を用意する。

これは、前Storybookを導入した時に感じた課題と同じだった。　Story作成

チームでの運用
ベストプラクティうす
ドキュメント、スニペット、ぷおロンぷと

変わったこと
実装前の共通認しk
品質っ鑑定

正常系が通らない状態での開発完了が亡くなった
問題の振り返りや薄さ

Issueがわかりやすくアンるってのはどうですかが

安定した開発サイクルのいじ

課題

Vテスっとパターンもれ
1 / 4 Story作成コスト


LLMフレンドリー
自律的にStoryを読み込んでくれたら確かに楽そう。



# 学生支援スポンサーセッション

11:30 - 12:00
### LINE公式アカウントの技術スタックと開発の裏側
#### speaker
LINEヤフー株式会社
フロントエンドエンジニア
佐野 友亮さん

LINE公式アカウントは、WEBアプリとしてフロントエンドエンジニアが作ってたりする。
LIEN Official Account Magner

Vueが多い、時点TS、JS

汎用的なコンポーネントの設計が求める


toly shadowdom
Light dom

webコンポーネント単位で分割している。
コンポーネントフローが悪い。
各フレームワークごとに、Wrapperがhituyou.

CUstomer Elements Manifest

CEM

CEM使って、ラッパーコンポーネントを作成。

CEMによって、Webコンポーネントの体験

Web Components

LINEヤフー
様々なカンファレンスに参加可能

最新技術を用いたチェレン人ぐな開発ができる

###  状況

SSRへの対応
SEOへの対応

### プレイドのユニークな技術とインターンのリアル
#### speaker
株式会社プレイド
ソフトウェアエンジニア
片山拓海さん


ユーザーを知るためにKARTEがいる

滑ると、 リアルタイムではJAva


フルスタック
一日のdeploy回数
23.15回
たくさんデプロイする

KARTE Blocks ABテスト できるツール。 Flex Editor.。

ノーコードで編集して、すぐにデプロイしてA/Bテストできたりするのは体験すごく良さそう。

Blitz

ブンさんキュー + キャッシュ設計

mila

  行動ログをBigQueryで解析していたが、柔軟な集計が難しい
  OLAP データベース「mila」を開発　すご
  

### Vue.jsを8年間使ってきた会社が今考えていること
#### speaker
Studio株式会社
フロントエンドエンジニア
齊藤広野

ノーコード。 Vueは1系から

vue1, vuex, js コード4万行

Vue3, pinia & vutex, ts コード52万行

Storeの Ba: 

bad practice
同じ画面という理由でまとめてStore
似た構造の関数なので、一つのStoreにまとめてある

good practice
関心ごとにStoreを分けましょう

bad practice
Actions内で値の生成も含んでしまっている。

good practice
値の生成はStoreの外部に
Storeは状態管理に徹底させる

bad  practice
component側でwatchして別のactionsを叩く

依存関係が複雑になり、挙動追跡が難しい。

good practice
watchは消して

副作用はactions内で完結

solid責務
- 依存関係逆転の法則(DIP)

実務で経験しないと出てこないような知見を聞くことができてよかった。




12:00 - 12:30
### kazuponが見つけた才能たち ─ Vue.jsそしてOSSエコシステムを変える開発者発掘術
#### speaker
株式会社プレイド
Vue.js コアチームメンバー
kazuponさん

株式会社メイツ
Vue.js メンバー
ubugeeeiさん

Vercel
Vue・Nuxt・Vite コアチーム
Anthony Fuさん

株式会社コドモン
ソフトウェアエンジニア
Naoki Habaさん

### 感想
OSSコミュニティの出会い

kazuponさん
vue.jsがリリースされたばっかの時会社で使う上でライブラリが足りなかった。
勉強会した

ドキュメントの更新から

Anthony Fuさん
OSSのモチベーションは下心だった。大学以外の場所で自分の能力を示したい。
kazuponさんが見つけてくれた

ubugeeeiさん
仕事で必要で触っていた。vueと出会い。レクチャーしたかった。
自分でVueを作ってみるハンズオン。 それをオープンにした方がいいのでは
スクラッチでVue作る

学生・社会人それぞれの始め方
kazuponさん
ライブラリを作ったが、敷居が高い
ドキュメント更新みたいなちっちゃいところから

楽しみながらやる。義務感があると辛い

Anthony Fuさん
自分のためにオープンソースやる。じゃないとモチベーションがない。
他人のためじゃない。

ubugeeeiさん
人と会って、OSSに貢献した。実際にやっている人と会って始める
けど、どうやって会うのか... → Vue Fesで会える！！！

コミュニティ活動の裏
Vue Fes Japan 運営の舞台裏・モチベーション

kazuponさん
定期的にVtokyoやっていた。
現状でやっていたコミュニティからスタート。そろそろカンファレンスやらないか

周りのメンバーに恵まれていた
ノウハウなくて、困ったがたまたまノウハウ持っている人がいた
会場代建て替えてくれる人がいた


順風満帆ではなかった(台風、コロナ...)

rebootイベント

Anthony Fuさん
みんなと出会い。
オープンソース機会でみんなと同じ場所に集まれる。
みんなにお礼を返す。

ubugeeeiさん
無給！

コミュニティ活動する
最初の一歩の踏み出し方


kazuponさん
給料が出ないからこそ、楽しみながらじゃないと続かない。
カンファレンスに参加するのとか、小さいイベント、ブログ書くも貢献になる.

Anthony Fuさん
tipsとして、熟成しているプロジェクトじゃなくて、新しいプロジェクトに参加する。
1000人のうちの1人か、10人のうちの1人なのか

ubugeeeiさん
意外とSNS見てるから、呟いてみると連絡きたりするよ！

# FitFits with hacomonoトラック

12:50 - 13:20
### フレームワークを超えて：次の10年のウェブを築く
#### speaker
Nuxt コアチームリード
Daniel Roeさん

13:35 - 14:05

#### 感想

挨拶が良い。
視聴者がリアクションを送れるのすごくユニーク

Nuxtではできない状況はほぼない。あったら教えてほしい。

ダウンロード数が落ちたのはnpmの障害か、あるいはクリスマスのせいか

AIの時代で、歯車のようなに何かに置き換えれるような感じがする。
↑これは事実ではない

web開発の職人。

ものを作る人々、使用するツールは変わる

取り残される恐怖がある。　
Twitterやblue skyなどのsns見れば新しい情報がたくさん出てるのが見れる。
新しいものを見るのは好きだが、時代送れるになってるという認識が生まれる。
ただ、見ている情報も世の中にある情報のほんの一部だけ。

web開発でむかつくもの
time、safari、css tailwind css...など

embrace a framewrok 
1. a framework is inevitable
2. a framework is desired
3. a framework is the right tool

webpackの設定は難しい。

ベストプラクティスとして体系的か
どのフレームワークを選択する

Nuxe font サードパーティフォンを の問題を解決するために、フレームワークレベルで追加した

フレームワークのいいところは、ベストプラクティスを他の人と共有できる。

早く行くなら一人で行け
遠くに行くならみんなで行け

nuxtの実験的な機能
複数のチャンクを一つのチャンクに統合する
仮想チャンクだったりの話

GitHubでアバターをみたことがあるけど、実際に会ったことない人に会えるのがVue Fes。

たった今、next 4.2がマージされた！？

とにかく、ユーモアがあるセッションだった。聞いてる側がリアクション送ったり、アンケート取ったりしながら話していたため30分とは思えないほど短く感じるセッションだった
あと、ライブマージはマジでビビった

### 5 年間における Vue 言語ツールの進化
#### speaker
Vue.js コアチームメンバー、Volar.js 作者
Johnson Chuさん

## 感想
元々ゲーム開発していた。

IDEサポート

VVScode HTML拡張きのと同様の方法で埋め込み言語をサポート。


TS speeed slow than tssever
no enought TS Support in template

volar TSレベルの開発者エクスペリエンsぬ
one lsp server for vue language support 
ts causes all lsp features slow

ts lspまちで、遅い待たないといけない


multiple lsp servers

TScが遅いって話？

ts
TS Plugin

Block TS Server origin language via plugin


Hugeメモリ

TSの拡張昨日から TS Serviを読み、vue server


vue extension、 vueserver、 ts

take over mode

not out othe box: 
development experience is downgrade in ts files

hybrid mode with named pipes
vue serverかrsyntax onlyだけ撮ってきて、 vue ts pluginかr 詳しい内容を持ってくることでメモリ使用量減らすし、速度の問題を改善する？


Hybid mode with Request forwarding







# フューチャーアーキテクトトラック

14:20 - 14:50
###  最高の DX - Nuxt Typed Router と Pinia Colada で実現する次世代 Vue/Nuxt 開発
#### speaker
株式会社メイツ
フロントエンドエンジニア
ナイトウコウスケさん

Composition API生まれ
script setup育ち

いいものにいいねとおう
コントリビューション。

Vueのエコシステム

Vibe Slide、AIと一緒に作っている
ライブラリから言語の未来を見ることができる。

textlint におこおこ。　主観的にで誇張的である

開発者の認知負荷を最小化
本質的な問題解決に集中


ピニャコラーダ、バージョン1nimo満たしてない


ボボウラープレートのような増えてしまう
型安全ルーティング

宣言的なデータフェッチ


Nuxt Typed Router
https://nuxt.com/modules/typed-router


IDEの補完効かない問題は開発体験悪そう Nuxt Typed Router使う解決できる

型安全あnナビゲーション

AIによるコードを静的解析を通して、レビュー漏れを防げる。 生成AI時代になって、静的解析できるってのはかなりアドバンテージなった気がするな

Nuxt Typed Pagesなるものがあるんだ

https://github.com/posva/unplugin-vue-router

pinia colada
tanstackに似ているなって思った

依存関係なし(pinia以外)
依存関係ないの偉い

mutation hooks
key設計が大事

# メイツトラック

15:05 - 15:35
### New Vue：サーバーサイドで動く WebAssembly/WASI プラットフォーム

### speaker
Cosmonic
バックエンドエンジニア
Victorさん

#### 感想
WASMについて、軽く説明

asm.js
c/ C++ JS

emscripten
c/C++ LLVM js subset code module


component model

c/c++/rust/js LLvm wasm components

幅広い言語をコンパイルしLLVM経由かバイナリ形式にコンパイルする

なぜwasm

理由はざっくり4つ
- Security
- Efficiency
- Robustness
- Open Source
言語サポートは増えている。

Rustがwasmサポートが手厚い、 goもTiny Goを通してサポートされている。
なぜ現代的なWASMと言っているのか

もう少し開発者にとって使いやすいもの

確かに、言語によって非同期の処理は変わったりするよね
調整するのが難しそう

WASMバイナリはどの部分にもアクセスできないし、ネットワークの通信ができない、ファイル読み込みもできない
WASMはDockerの用に静かに登場し、気づいたら普及している基礎技術
↑これはそうだなと感じていて、徐々にWASMを聞く機会が増えてきてる

15:45 - 16:15
### Demystifying Nuxt Test Utils
#### speaker
STORES 株式会社
デザインエンジニア / nuxt ecosystem team
wattanx

#### 感想
@nuxt/test-utilsがないとNuxtアプリケーションのテストは難しい

DefinePagemeta
ページコンポートのメタデータを設定できる

import していない → Auto import。components, composables, utilsによるコンポーネントや関数はimport文なしで読み取れる

UnimportでAutoインポートできる。

https://github.com/nuxt/test-utils

そもそも、Nuxt環境とは

- Nuxt Modulesが動いている
(Auto ImportやdefinePageMetaの設定)
- PluginsやMiddlewareが動いている
- NuxtのContextが利用できる

vite:configResole hookでNuxtが設定しているvite pluginがVitestに

Nuxtアプリケーションの流れ
entry.jsからアプリ作成、プラグイン適応、マウント

テスト実行時
run test
run setup files
setupNuxt()から

アプリ作成、プラグイン適応、マウント

つまり、アプリケーション実行時と同じ流れで処理している

mountSuspended

routeを指定すると、ページ遷移が再現されため、middlewareが動く

SSR

setup使うとdev serverが立ち上がりテストできる。
vitestなの、playwrightのAPIを使ってるのおもろいな

tips

nuxtに依存しないVueコンポーネントはjsdomやhappyodom
vueに依存しない node

実装を変更すると動作は同じなのにテストが壊れる
→実装の詳細に依存しないテストを書くべき

まとめ
Nuxtアプリケーションのテストは@nuxt/test-utils使うと簡単にかける

フロントエンドのテストをあまり書いたことないから、テストの書き方みたいなところで参考になるセッションだった


16:25 - 17:25
### なんでRustの環境構築してないのにRust製のツールが動くの？
#### speaker
株式会社ZOZO
フロントエンドエンジニア
ssssotaさん
### Nuxt4のSingleton Data Fetching Layerで何が変わるのか

#### speaker

株式会社 コドモン
ソフトウェアエンジニア
Naoki Habaさん
### アウトプットから始めるOSSコントリビューション 〜eslint-plugin-vueの場合〜
#### speaker
弁護士ドットコム株式会社
フロントエンジニア
ツノさん

### 知覚とデザイン short version
#### speaker
エンジニア
Rinchokuさん
### Nuxt 認証基盤作成における Cookie 状態管理のポイント
#### speaker
フリーランス
フロントエンジニア
shiminoriさん
### 個人でデジタル庁のデザインシステムをVue.jsで作っている話
#### speaker
株式会社ICS
フロントエンドエンジニア
にしはらさん
### React Nativeならぬ"Vue Native"が実現するかも？ 新世代マルチプラットフォーム開発フレームワークのLynxとLynxのVue.js対応を追ってみよう
#### speaker
フューチャーアーキテクト株式会社
シニアコンサルタント
永井優斗さん
### chocoZAPサービス予約システムをNuxtで内製化した話
#### speaker
RIZAPテクノロジーズ株式会社
フロントエンドエンジニア
Kaede Katoさん
