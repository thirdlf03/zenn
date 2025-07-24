---
id: 33-zenn-what-is-neovim
aliases: 
tags: 
autonotemover: disable
cssclasses:
  - zenn
date: 2025-07-17
published: false
title: Neovimとは
type: tech
url: https://zenn.dev/thirdlf/articles/32-zenn-what-is-neovim.md
---
# Neovimとは？

hyperextensible Vim-based text editor
https://neovim.io/

Neovim is a project that seeks to aggressively refactor [Vim](https://www.vim.org/) in order to:

- Simplify maintenance and encourage [contributions](https://github.com/neovim/neovim/blob/master/CONTRIBUTING.md)
- Split the work between multiple developers
- Enable [advanced UIs](https://github.com/neovim/neovim/wiki/Related-projects#gui) without modifications to the core
- Maximize [extensibility](https://neovim.io/doc/user/ui.html)

https://github.com/neovim/neovim
簡単に言えば、拡張性の高いVimみたいなエディターです。

## Neovim本体のインストール方法
ここを参照
https://github.com/neovim/neovim/blob/master/INSTALL.md

### Windows
```bash
winget install Neovim.Neovim
```

### Mac
```bash
brew install neovim
```

### Linux
省略

## LazyVimを導入してみよう。
インストールしたばかりのNeovimは、Vimとあまり変わらないのでNeovimをフルで使うためにLazyVimを導入します。

### LazyVimとは
パッケージマネージャーであるlazy.nvimを使い、いろんなプラグインをセットアップしてくれたりするやつです。

LazyVim is a Neovim setup powered by [💤 lazy.nvim](https://github.com/folke/lazy.nvim) to make it easy to customize and extend your config.

https://www.lazyvim.org/

自分好みにカスタマイズしていく楽しみもありますが、まずはLazyVimで環境を作ってみましょう。

## セットアップ手順
以下を参照
https://www.lazyvim.org/installation

## Mac

```bash
# required  
mv ~/.config/nvim{,.bak}  
  
# optional but recommended  
mv ~/.local/share/nvim{,.bak}  
mv ~/.local/state/nvim{,.bak}  
mv ~/.cache/nvim{,.bak}
```

```bash
git clone https://github.com/LazyVim/starter ~/.config/nvim
rm -rf ~/.config/nvim/.git
nvim
```

## 便利なキーマップ

ファイルタブ開く
<leader>e

ファイル検索
<leader>f

全探索
<leader>g


# LSP(Language Server Protocol)
2016年にMicrosoftによって発表された開発支援ツールのためのプロトコルで、
静的解析ツールやリンター、フォーマッターのようなツールとエディタとの連携を行う

LSPマネージャーを開く
<leader>cm

<C-f> で自分のやりたい言語検索

