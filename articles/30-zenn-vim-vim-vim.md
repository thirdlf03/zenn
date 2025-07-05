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
published: false
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

**もう疲れたよ！！！！**

いや、トレンドを追いかける楽しさはあるけれども...

そんなAI疲れしたプログラマーに、Vimを使ってコードを書く楽しさを伝えたい！！！(伝われ〜〜〜）

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


他にも、練習できるサイトがあるので遊んでみるのがおすすめです。
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

## 移動系
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


## 編集系
まずは、削除に関するものから触っていきましょう。

Hello World. のWにカーソルを合わせてください。
その状態で、dw と入力してみましょう。
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello World.
aaa
```
って状態になったと思います。uを押すと操作を取り消すことができるので、uを押してみましょう。
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
```md:vimmer.md
Vim is the best editor in the world. My favorite editor is Vim.
Hello World.
aaa
```

感の良い方なら、ある法則を見つけていると思います
## オペレータとモーション、カウント
削除の時、 d (delete ) と wやb、$など移動系で使ったコマンドを組み合わせていましたよね？

d のように、削除などの操作を行うものは**オペレータ**と呼ばれまてす。
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

ex)
d2w 現在のカーソル位置から、2つ次の単語の位置まで削除

を意味していました。

このように応用がきくところが、Vimの好きなところです




























