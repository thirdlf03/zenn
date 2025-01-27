---
published: false
cssclass: zenn
type: "tech"
emoji: "💡"

title: "uvを使って、FastAPIの環境を作ってみる"
topics: []
date: 2025-01-27
AutoNoteMover: disable
url: "https://zenn.dev/thirdlf/articles/11-zenn-uv-fastapi-prisma"
tags: [" #type/zenn "]
aliases: 
---

# きっかけ
uvってのがあるってのは知っていて、実際に使ってみて感動したのでこれを使ってFastAPI
環境整えようと思いました。

# 使用するライブラリ  
- FastAPI
- IceCream
- Pyright
- Prisma Client Python

# uvとは
uvは、Rust製のパッケージマネージャーでとにかく爆速でいい感じです。

使用するのも簡単で、curlやHomebrewでセットアップできる。

curlの場合
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

wgetの場合
```bash
wget -qO- https://astral.sh/uv/install.sh | sh
```

Homebrewの場合
```bash
brew install uv
```

なお、Dockerのイメージもある。

# uvを使ってみる

venv作る
```
uv venv
```

超簡単
activateするには
```
source .venv/bin/activate
```
or
```
. .venv/bin/activate
```

でactivateできる

