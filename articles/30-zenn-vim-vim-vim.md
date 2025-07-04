---
id: 30-zenn-vim-vim-vim
aliases: 
tags:
  - vim
autonotemover: disable
cssclasses:
  - zenn
date: 2025-06-03
emoji: 🛜
published: false
title: 生成AI?うるせぇVimだVim！Vimを使ってコードを書くんだ！！！
type: tech
url: https://zenn.dev/thirdlf/articles/30-zenn-vim-vim-vim
---

:::message
この記事は全てVimを使って書かれています。
:::

# 生成AI進化しすぎ
最近、やれClaude CodeやGemini CLI、CursorやGitHub Copilot、Cline、Roo Code、Windsurf、Devin、Amazon Q Developer CLI、OpenHands CLI、Codex、Gemini 
Code Assistなどいろんな生成AIを用いたコーディング支援ツールが出てきてますよね？
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

Vimを起動して練習してみましょう。ただ、ある程度文章がないと練習しがいがないので、vimtutorってものを起動してもらいます。

```zsh
vimtutor ja
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







