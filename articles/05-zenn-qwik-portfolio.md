---
published: false
cssclasses:
  - zenn
type: tech
emoji: 🚀
title: Qwikで爆速でつよつよなポートフォリオサイト作る
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

# 解説
githubのリポ
https://github.com/thirdlf03/portfolio


## デプロイ
今回は、Cloudflare Pagesでホスティング
```bash
npm run qwik add
```
で Adapter: Cloudflare Pagesを導入

