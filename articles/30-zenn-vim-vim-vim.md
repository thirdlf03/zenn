---
id: 30-zenn-vim-vim-vim
aliases: 
tags:
  - vim
autonotemover: disable
cssclasses:
  - zenn
date: 2025-06-03
emoji: 👍
published: true
title: 生成AI？うるせぇVimだVim！Vimを使ってコードを書くんだ！！！
type: tech
url: https://zenn.dev/thirdlf/articles/30-zenn-vim-vim-vim
---

:::message
この記事は全てVimを使って書かれています。
:::

# 生成AI進化しすぎ
最近、やれClaude CodeやGemini CLI、CursorやGitHub Copilot、Cline、Roo Code、Windsurf、Devin、Amazon Q Developer CLI、OpenHands CLI、Codex、Gemini Code Assistなどいろんな生成AIを用いたコーディング支援ツールが出てきてますよね？
モデルも、Gemini 2.5 ProやClaude Opus 4、o3 proなどAIの成長が止まらないし...

**正直、もう疲れたよ！！！！**

いや、トレンドを追いかける楽しさはあるし、ものを作るのが好きな人間なので思いついたものをすぐ作れるのは楽しいけどね...

そんなAI疲れしているプログラマーの方ってちらほらいる気がします

加えて、生成AIの便利さゆえに
「自分の手でコードを書く楽しさ」を忘れてしまった人、
そもそも楽しさを味わえずにいる初学者も増えている気がする...

そんな方々に、**Vim**でコードを書く楽しさを伝えたい！！！！
伝われ〜〜〜！！って記事です

# まず、Vimをインストールしよう

## Mac
デフォルトでインストールされてます(だって、**標準のエディター**だからね)
ターミナルで、
```zsh
vim
```
って入力して何も出てこない場合、Homebrewでインストールしましょう。
```zsh
brew install vim
```

## Windows
~~導入結構めんどくさい~~Windows 11だと、デフォルトでwinget入っているらしい
```powershell
winget install Vim.Vim
```

知らぬ間に便利になってる

## Linux
省略

# まずは、呼吸
早速、Vimを触っていきたいんですが、これより先マウス(トラックパッドもだよ)の使用は原則禁止です。

じゃあ、どうやってカーソル移動するのって思われる方いると思います。

Vimでは、hjklを使ってカーソルを移動します。

             ^
             k              
       < h       l >                
             j                       
             v

はい。 hは左、jは下、kは上、lを右です。

これはVimを使う上では呼吸するように使えないといけない(は言い過ぎだけど) のでひたすら練習しましょう！

ただ、ある程度文章がないと練習しがいがないので、vimtutorってものを起動してもらいます。

## vimtutorとは
vimをインストールした時に付属しているやつで、 Vimの一通りの操作を学ぶことができます。
この後の文章無視して、vimtutorをどんどん進めてもらっても構いません！！
ただし、デフォルトだと英語なのでjaとつけて日本語で起動しておきます。

```zsh
vimtutor ja
```

内容は一緒なので、英語でやりたいって方は
```zsh
vimtutor
```

満足行くまで練習できたら、ESCキー押して

```zsh
:q!
```

と入力しましょう！


他にも、hjkl移動を練習できるサイトがあるので遊んでみるのがおすすめです。
https://vimate.jp/lessons/vimmer-01

ちなみに僕のスコアです
![](/images/vim1.png)

# 便利なコマンド達
ようやくVimのなかで呼吸ができるようになったので、もう少し本格的にVimを使っていきましょう。

その前に、説明する必要があるVim特有の概念があります。

## モード
モードと呼ばれる概念がVimには存在します。
先ほど、hjklでカーソルを移動すると書いていたと思います。VSCodeだったら、hjklって入力するとhjklってエディター上に入力されますよね？

これはモードという概念が関係して、Vimは起動時ノーマルモードからスタートします。

ノーマルモードでは、文章を挿入することは基本的にできず、コマンドを入力するだけことしかできません。
え！不便じゃんって声が聞こえてきました。いいえ、このノーマルモードがあるからこそVimは便利なんです。

早速、体感してもらいましょう。

まず、適当なファイルを作っていいディレクトリに移動し
```zsh
vim vimmer.md
```
と入力しましょう！

無事Vimが起動できたら、文章を入力してもらいます。

文章を入力するときは、インサートモード(iやaキーを押すと切り替えられる)に変更します。

切り替えることができていたら、左下に -- INSERT -- とでています。

文章入力しましょう。
```md:vimmer.md
Vim is the best editor in the world.
```

# 移動系
入力完了したら、ESCキーを入力しノーマルモードに戻ります。
ノーマルモードに移行後、VimのVにカーソルを合わせて w を押してみましょう。
wを押すと、次の単語に飛ぶはずです。前の単語にいきたい場合は、bを押しましょう。

次は、VimのVにカーソルを合わせて、eを押してみましょう！
wと少し挙動はしていますが、eは、単語の終点位置に移動するって形になっています。
VimのVだったら、mまで移動。もし、終端にいる場合は次の単語の終端に移動します。

wやb, eを連打するのがめんどくさかったら、 数字 + w と入力してみましょう。
2w, 2b, 4eなど

次に、.にカーソルを合わせて 0 を押した後、$ を押してみましょう！
0だと行頭、$だと行末に移動できます。

次は、world.の後に文章を追加していきます。
Vにカーソルを合わせた状態で、A (大文字のa)を入力してください。
すると、行末まで移動した上でインサートモードに入ったと思います。

以下のように文章を追加しましょう。
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello World.
aaa
```

入力できたらESCでノーマルモードに切り替えてください。

次は、ggと入力してみましょう！
1行目に移動したと思います。 数字 + ggで任意の行に飛ぶことができます

次は、Gと入力してみましょう！

そうすると最終行に飛ぶと思います。

## 移動系のまとめ
| キー         | 内容                                                                    |
| ---------- | --------------------------------------------------------------------- |
| h, j, k, l | 左から←↓↑→に移動<br>vimでの移動の基本                                              |
| w          | 次の単語に移動                                                        |
| b        | 前の単語に移動 |
| e        | 単語の終端 (end) まで移動 |
| num + w, b, e | numの数字分、wやb、eを繰り返す | 
| 0 | 行頭まで移動 |
| $ | 行末まで移動 |
| gg         | 一番上に移動                                                                |
| num + gg | numの数字の行まで移動する |
| G       | 最終行まで移動             |

主要な移動系コマンドを触っていきました。
もうすでに、Vimの虜になってきていると思います。やったね

続いてお待ちかねの編集系のコマンドを触っていきます。

その前に一旦保存しましょう！
:w で保存できます。
もし、休憩したい場合
:wqで保存した後、Vimを終了することができます。


# 編集系
まずは、削除に関するものから触っていきましょう。

## 削除系
Hello World. のWにカーソルを合わせてください。
その状態で、dw と入力してみましょう。
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello .
aaa
```
って状態になったと思います。u (undo) を押すと操作を取り消すことができるので、uを押してみましょう。
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello World.
aaa
```
Hello World. に戻ったと思います。

引き続きWに合わせた状態で、dbと入力してみましょう。
すると、
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
World.
aaa
```
になったと思います。uで戻します
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello World.
aaa
```

今度は、Wにカーソルを合わせてd$と入力してみましょう。
すると
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello 
aaa
```

となったはずです。uで戻しますね
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello World.
aaa
```

次は、HelloのHにカーソルを合わせた状態で d3wと入力してみましょう！
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.

aaa
```

全部消えてしまいましたね。 uで戻します。

次は、Hello World.のどこかにカーソルを移動し、ddと入力してみます。
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
aaa
```

行全体が消えたと思います。uで戻した後、2ddと入力してみましょう
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
```

2行分消えましたね！uで戻しておきましょう。

感の良い方なら、ある法則を見つけていると思います
# オペレータとモーション、カウント
削除の時、 d (delete ) と wやb、$など移動系で使ったコマンドを組み合わせていましたよね？

d のように、削除などの操作を行うものは**オペレータ**と呼ばれてます。
wやb、$のように、移動に関するものは**モーション**と呼ばれてます。

つまり、
オペレータ + モーションでやりたいことを実現できます。

なので、先ほど削除で使っていたコマンドは

dw 現在のカーソル位置から、次の単語の位置まで削除
db 現在のカーソル位置から、前の単語の位置まで削除
d$ 現在のカーソル位置から、行末まで削除

を意味しています。

さらに、
オペレータ + カウント + モーション と指定することもできます。
カウントとは、2wのようにモーションや特定の動作の前につける数字のことです。

d2w 現在のカーソル位置から、2つ次の単語の位置まで削除

を意味していました。

また、オペレータにはもう一つ特徴があり、ddのように2回繰り返すと行全体と指定することができます。
もちろん、カウント + ddも可能です。
後述のオペレータである、cやyも同じことが言えます。


このように応用がきくところが、Vimの好きなところです

まだ紹介していない、削除系のコマンドを使っていきます。

HelloのHにカーソルを合わせて、xを押してみましょう。

```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
ello World.
aaa
```
一文字削除されたと思います。 そのまま5xと入力してみましょう。

```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
World.
aaa
```

xが5回繰り返されましたね！ ここで、U(大文字のu) を入力してみましょう。

```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello World.
aaa
```
大文字のUは、カーソルの行で行われた変更をまとめて戻すことができます。


### 削除系コマンドまとめ
| キー         | 内容                                                                    |
| ---------- | --------------------------------------------------------------------- |
| d + モーション (d + カウント + モーション)         | モーションでどこまで削除するのか指定して、削除|
| x | カーソルの位置の文字を削除する|
| dd       | 行全体を削除             |

## 編集系
c というオペレータを使っていきます。
WorldのWにカーソルを合わせて、ce と入力してみましょう。
すると、単語を削除した上でINSERTモードに移行したと思います。任意の文字を入れてみましょう。
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello thirdlf.
aaa
```

次に、Hello ~~の行にカーソルを合わせてoと入力してみましょう。
すると、１行改行したあとINSERTモードに移行したと思います。何も入力せずにESC押しましょう。
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello thirdlf.

aaa
```

また、Hello ~~の行にカーソルを合わせて今度は O と入力してみましょう。
すると、１行上に移動した上でINSERTモードに移行したと思います。便利ですね
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.

Hello thirdlf.

aaa
```
このoやOは、かなり便利なので覚えておきましょう！

### 編集系コマンドまとめ
| キー         | 内容                                                                    |
| ---------- | --------------------------------------------------------------------- |
| c + モーション (c + カウント + モーション)         | モーションでどこまで削除するのか指定して、削除したあとINSERTモードに入る|
| o       | １行下に改行して、INSERTモードに移行            |
| O | １行上に改行して、INSERTモードに移行 | 

## 置換系
aaaの先頭にカーソルを合わせて、rxと入力してみましょう。
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello World.
xaa
```
aがxに置き換わったと思います。rは現在のカーソル位置の一文字を任意の文字に置き換えるコマンドです。

次は、xにカーソルを合わせた状態で3rあ と入力してみてください。

```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello World.
あああ
```
xaaが あああに変わりましたね。

次は、WorldのWにカーソルを合わせて Rを押してみましょう！
大文字のRを押すとReplaceモードに移行します。
適当に文字を入力してみましょう。今回は、Vimmer.と入力してみます。
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello Vimmer.
あああ
```
Replaceモードに入ると、ESC押すまで入力した文字を置き換えていきます。

### 置換系コマンドまとめ
| キー         | 内容                                                                    |
| ---------- | --------------------------------------------------------------------- |
| r + 任意の一文字| カーソル位置の一文字を任意の文字に置換|
| R | REPLACE MODEに移行します。ノーマルモードに戻すにはESC|

### おまけ(sed)
Vim is ~~って書いている行のどこかにカーソルを持っていき、
:s/Vim/VSCode/g と入力してみましょう。

```md:vimmer.md
VSCode is the best editor in the world. My favorite editor is VSCode.
Hello Vimmer.
あああ
```

sedに詳しい人は、これが一番早いです。

## コピー & ペースト
もちろん、コピペもできます。
yを使ってコピーしていきます。yはオペレータです。
VimmerのVにカーソルを持っていき、yeと入力します。
入力後、pを押してみましょう。 すると、現在のカーソルの位置にVimmerって単語が貼り付けられるはずです。
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello VVimmerimmer.
あああ
```
Vimでは、コピーのことをヤンク(yank)と呼びます。なので、yを使っています。

もちろん、オペレータなのでyyと入力すると行全体コピーしたり、3yyで現在のカーソル位置から3行下にヤンクできます。

次は、VISUALモードを使ったヤンクをやってみましょう。

aaaのどこかにカーソルを持っていき、Vと入力しましょう
すると、VISUALモードと呼ばれるモードに移行します。
その状態で、kを押してVim ~~の行まで選択します。選択できたら、yを押してヤンク。
pを押して貼り付けてみましょう。

```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello Vimmer.
aaa
Vim is the best editor in the world. My favorite editor is Vim.
Hello Vimmer.
aaa
```

視覚的にどこをヤンクするか確かめるのに、VISUALモードは役に立ちます！

### コピー & ペーストコマンドまとめ
| キー         | 内容                                                                    |
| ---------- | --------------------------------------------------------------------- |
| y + モーション (y + カウント + モーション)         | モーションでどこまでヤンクするのか指定して、ヤンクする|
| p       | ヤンクした内容を貼り付ける|
| V | VISUALモードに移行する | 

# いざ、実践！
ここまで学んだ知識を使って、Vimmerの編集速度を体感してもらう問題を用意しました！
```bash
vim problem.md
```
以下の内容を、mdファイルに貼り付けてください。 ctrl + v (cmd + v)で貼り付けることができます。

```md:problem.md
name = "hoge"

def vimmer(name: int) -> str:
   printf("Welcome to vim hoge world, name")

```

このファイルを、以下のような状態にしたいです。
```md:problem.md
name = "thirdlf"

def vimmer(name: str):
   print(f"Welcome to Vim world, {name}")

vimmer(name)
```

どのように入力したら早く編集できそうか、考えてみてください！

:::details 入力例
あくまで一例です。
```
gg → 3w → ce → thirdlf → ESC → 3gg → 5w → R → str → ESC → 2l → d3w → e → x →
 a → f → ESC → 5w  → dw → 2w → i → { → ESC → e → a → } → ESC → G → o → vimmer(name) で完了
```
:::

# 終わりに
Vimの魅力伝わりましたか？ ここまで記事を読んでくださった方々は、もうVimの虜になっていることでしょう。

しかし、この記事で書いている内容はあくまでチュートリアルにすぎません。

この先は、もっとVim使えるようになりたい！ 普段のエディターVimにしたい！って方向けの情報です。
## vimtutor
冒頭でも紹介しましたが、公式のチュートリアルがございます。この記事で紹介していない内容も数多く含まれているので、是非やってみてください！

```bash
vimtutor
```

で起動できます。

日本語版も存在します！
```
vimtutor ja
```

## Neovim
普段のコーディングでVimを使いたいとなった場合、最近はNeovimというエディターが主流です。
僕も普段使いしているのは、neovim(nvim)です。

以下の記事が参考になります。
https://zenn.dev/vim_jp/articles/1b4344e41b9d5b

公式URL
https://neovim.io/

## VimConf
年に一度行われるVimのカンファレンスです。
今年は、11月2日 アキバプラザ・アキバホールでVimConf 2025 Smallが行われる予定です。

https://vimconf.org/2025/ja/

行きたいな...

## Vim駅伝
Vimが好きな方々が集まり、リレー形式で記事を書いているアドベントカレンダーのようなものです。
暇な時に覗いてみると、新しい発見があって面白いです！

https://vim-jp.org/ekiden/

## Vimium
Chromeの操作をVimライクにできる拡張機能です！
使いこなせると普段のブラウザ操作がかなり快適になるので、おすすめです。

https://zenn.dev/mkobayashime/articles/vimium-vim-browser



楽しいvimライフを〜〜〜
