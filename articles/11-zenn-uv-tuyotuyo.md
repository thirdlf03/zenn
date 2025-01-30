---
published: true
cssclasses:
  - zenn
type: tech
emoji: 💡
title: uvを布教したい!!!
topics: 
date: 2025-01-27
AutoNoteMover: disable
url: https://zenn.dev/thirdlf/articles/11-zenn-uv-fastapi-prisma
tags:
  - "#type/zenn"
aliases:
---

# きっかけ
uvってものがあるのは知ってて、使ってみるとその速度に感動しました。
これは布教したいと思ったので記事を書きます。


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

# uvのいいところ
## venvの作成速度が爆速
```
uv venv
```

超簡単で早い
activateするには
```
source .venv/bin/activate
```
or
```
. .venv/bin/activate
```

でactivateできる

## ライブラリのインストールが爆速
```bash
uv pip install ~
```
もしくは
```bash
uv add ~
```

でインストール可能

まじで早い

もちろん
requirements.txtからも可能

```bash
uv pip install -r requirements.txt
```
or
```bash
uv add -r requirements.txt
```

## uv syncが便利すぎる
```bash
uv init
```
するとpyproject.tomlができるんですが、
これがあるディレクトリで
```bash
uv sync
```
するだけで、venvやライブラリが準備される。

便利すぎる

## 公式ドキュメントが充実してる
ありがたい
やりたいこと大体書いてある

# 具体的な使用例

まず、任意のディレクトリでinitする
```bash
uv init
```

すると
- .git
- .gitignore
- .python-version
- README.md
- hello.py
- pyproject.toml

が作成されます。

hello.pyを実行するには
```
uv run hello.py
```
でvenvが作成されて、実行されます。

例えば、FastAPIを使いたいってなったら
```bash
uv add fastapi
```
--devでdev dependenciesに入れることができます。

pytestを入れてみましょう
```
uv add --dev pytest
```

簡単ですね

やっぱりFastAPIいらないってなったら
```
uv remove fastapi
```

ruffのようなtool使う場合は、
```
uv tool install ruff
```

使う場合は
```
uvx ruff check
```
or

```
uv tool run ruff
```

のように使えます。

チームで開発する場合、cloneしてきた場所で
```
uv sync
```

で環境を揃えられます。


# まとめ
ざっくりですが、uvのいいところを書いていきました。
一部の機能しか書いていないので、気になった方はぜひ公式ドキュメントを覗きにいってください！！

https://docs.astral.sh/uv/

