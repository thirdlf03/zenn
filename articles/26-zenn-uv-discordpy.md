---
id: 26-zenn-uv-discordpy
aliases: 
tags: 
autonotemover: disable
cssclasses:
  - zenn
date: 2025-05-04
emoji: 📕
published: false
title: uv + ruff + tyを使ったモダンな環境でdiscord bot作ってみよう
type: tech
url: https://zenn.dev/thirdlf/articles/26-zenn-uv-discordpy
---
# 記事の趣旨
今回は、Astral社のツールを使ったモダンな環境でdiscord botを作っていこうという趣旨の記事です。


## Astral社とは
Astral社は、uvやruff、tyなど次世代のPythonツールを作っている会社です。
https://astral.sh/

この会社が作っているツールのおかげで、Pythonの開発体験がかなり良くなっています。今回のbot開発を通して、それを感じてもらえたら幸いです


# uvとは
pythonのパッケージマネージャーで、Rustで書かれています。
[10-100x faster](https://github.com/astral-sh/uv/blob/main/BENCHMARKS.md) than `pip`
と公式で書いてある通り、爆速です。

# まずはuvのインストールから
好きなもので大丈夫です。

### macOS and Linux
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### windows
powershellで
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

### Homebrew
```bash
brew install uv
```

### Cargo
```bash
cargo install --git https://github.com/astral-sh/uv uv
```

## Docker
ここには詳しく書かないです。

参考記事
https://docs.astral.sh/uv/guides/integration/docker/

公式イメージだとFastAPI使う時に、pydantic coreで引っかかったので Ubuntu OS使ってのsetupも置いときます
https://github.com/thirdlf03/uv-fastapi


# 環境構築
まずはuv initしましょう。
今回は、example botって名前でディレクトリ作成します
```bash
uv init example-bot
```

移動
```bash
cd example-bot
```

必要なライブラリやツールをインストール
```bash
uv add discord.py ruff ty python-dotenv
```

## ruff
高速なLinter & Formatter

## ty
型チェッカーで、mypyと比べてかなり高速になっているという噂


## discord.py
pythonのDiscord用のAPIラッパーで、pythonでdiscord bot作るのに使います。


## botの準備
discord botを作っていく前に、botを準備しましょう

まず、開発者ポータルにアクセスしてログインする
https://discord.com/developers/docs/intro


ログインできたら、applicationページに飛ぶ
https://discord.com/developers/applications

OAuth2のタブに行って、下の方にURL Generatorって項目があるのでbotを選択し、権限は適当なものを選びましょう


サーバーにbotを追加できたら、次はbotを動かすためのtokenを取得します。

botにタブに移動して、tokenって項目があるのでそこからtokenコピーしましょう。
もし、reset tokenと書いてあればresetするとtokenが出てきます。

:::message alert 
このtokenは外部に漏らさないように!!!!
:::

サーバーにbot追加とtokenがコピーできたら、早速botを動かしてみましょう。

参考記事)
https://discordpy.readthedocs.io/ja/stable/discord.html

# Hello world
本題に入っていきます。
まずは、ユーザーの発言したら挨拶をするようにしましょう。

まず、discord.pyをimportしてbotを起動します

main.py
```python:main
import discord
from dotenv import load_dotenv
import os

load_dotenv()

bot_token = os.getenv("BOT_TOKEN")

intents = discord.Intents.default()  

client = discord.Client(intents=intents)

client.run(bot_token)
```

.envファイル作る

.env
```
BOT_TOKEN="your_token"
```


.gitignore
.envをgitignoreに追加
```bash
# Python-generated files
__pycache__/
*.py[oc]
build/
dist/
wheels/
*.egg-info

# Virtual environments
.venv

.env
```

botを起動してみましょう。
```
uv run main.py
```

discordでbotが起動しているか確認

次に、メッセージに反応してHello worldと発言するようにする。
```python:main
import discord
from dotenv import load_dotenv
import os

load_dotenv()

bot_token = os.getenv("BOT_TOKEN")

intents = discord.Intents.default()
intents.message_content = True

client = discord.Client(intents=intents)

@client.event
async def on_message(message):
    if message.author == client.user:
        return
    if message.content.startswith('$hello'):
        await message.channel.send('Hello world!')


client.run(bot_token)
```

この状態で、botを起動してチャットで$helloと入力するとHello!と返ってくるはずです。

ざっくり解説すると、

ここで必要なライブラリのインポートとenvからTokenを取得しています
```python:main
import discord
from dotenv import load_dotenv
import os

load_dotenv()

bot_token = os.getenv("BOT_TOKEN")
```

botの設定をして、Clientを初期化します。
```python:main
intents = discord.Intents.default()
intents.message_content = True

client = discord.Client(intents=intents)
```

botが発火するためのeventを設定していきます。
今回は、メッセージを検知したらイベントが発火してほしいので on_messageを使っています。
```python:main
@client.event
async def on_message(message):
```

メッセージ主がclient.user(bot)だったら無視して、もしメッセージの中身が$helloから始まっていたら、そのメッセージが入力されたチャンネルにHello !と返すようになっています。
```python:main
    if message.author == client.user:
        return
    if message.content.startswith('$hello'):
        await message.channel.send('Hello world!')
```

client.runでbot起動する
```python:main
client.run(bot_token)
```

詳しくは
https://discordpy.readthedocs.io/ja/latest/quickstart.html

ここで区切りなので、一旦コミットしていきましょう。
コミットする前にLintとformatter、型チェックを行なっていきます。
```bash
uvx ruff check
```

```bash
uvx ruff format
```

```bash
uv ty check
```

と行なっていくと、tyでエラーが発生すると思います。
```
 uvx ty check                                                                                   3m38s✨   16:40 
Installed 1 package in 7ms
WARN ty is pre-release software and not ready for production use. Expect to encounter bugs, missing features, and fatal errors.
error[invalid-argument-type]: Argument to bound method `run` is incorrect
  --> main.py:23:12
   |
23 | client.run(bot_token)
   |            ^^^^^^^^^ Expected `str`, found `str | None`
   |
info: Function defined here
   --> .venv/lib/python3.12/site-packages/discord/client.py:826:9
    |
824 |         await self.connect(reconnect=reconnect)
825 |
826 |     def run(
    |         ^^^
827 |         self,
828 |         token: str,
    |         ---------- Parameter declared here
829 |         *,
830 |         reconnect: bool = True,
    |
info: rule `invalid-argument-type` is enabled by default

Found 1 diagnostic
(example-bot) 
```

これはbot_tokenがstrを期待しているのに対して、str | Noneになってしまっているからですね
型ヒント与えましょう。

```python:main
bot_token: str | None = os.getenv("BOT_TOKEN")
if bot_token is None:
    raise ValueError("BOT_TOKEN environment variable not set")
```

この状態で、
```bash
uvx ty check
```
すると
```bash
❯ uvx ty check                                                                                      0s✨   16:46 
WARN ty is pre-release software and not ready for production use. Expect to encounter bugs, missing features, and fatal errors.
All checks passed!
(example-bot) 
```

通りましたね。嬉しい
わざわざPythonで型つける必要あるの？って思うかもしれませんが、機械学習だったり、規模が大きいプロジェクトになってくるとどうしても型をつけないとやってられません。
機械学習で長時間学習させる時に、型エラーで実行中断されてしまったら目も当てられないです。

余談はさておき、これでlint、format、型チェックが通ったのでコミットしましょう。
```
git add .
git commit -m "~~~"
```

# Hello world改
ただ、メッセージを表示されるだけだと面白くないので改良していきましょう。

まずは、メッセージの主にメンションする & スタンプを使うようにする。
メンションは、message.author.mention
絵文字は、絵文字idを指定すると使えます。

絵文字idは、絵文字を右クリックすることで確認することができます。
もしidが出ない場合は、開発者モードにしましょう。
```python:main
@client.event
async def on_message(message):
    if message.author == client.user:
        return
    if message.content.startswith("$hello"):
        emoji_id = 1375023893563310090
        custom_emoji = f"<:emoji_name:{emoji_id}>"
        await message.channel.send(f"{message.author.mention} Hello world! {custom_emoji}")
```

次に、メッセージ入力中と出てくるようにしましょう。
typing()を使うことで入力中にすることができます。デフォルトの秒数が短いので、5秒ほどまってから入力するようにします。

```python:main
import asyncio

~~~
省略
~~~

@client.event
async def on_message(message):
    if message.author == client.user:
        return
    if message.content.startswith("$hello"):
        emoji_id = 1375023893563310090
        custom_emoji = f"<:emoji_name:{emoji_id}>"
        async with message.channel.typing():
            await asyncio.sleep(5)
        await message.channel.send(f"{message.author.mention} Hello world! {custom_emoji}")
```

これで、5秒間入力状態になった後にメッセージを送信するようになりました。


次に、$helloと入力したユーザーのメッセージにリアクションするようにしてみましょう。
add_reactionでreactionつけることができます。

```python:main
@client.event
async def on_message(message):
    if message.author == client.user:
        return
    if message.content.startswith("$hello"):
        await message.add_reaction("👍")
        emoji_id = 1375023893563310090
        custom_emoji = f"<:emoji_name:{emoji_id}>"
        async with message.channel.typing():
            await asyncio.sleep(5)
        await message.channel.send(f"{message.author.mention} Hello world! {custom_emoji}")
```

次は、ユーザーに返信する形にしてみましょう。
sendのreferenceにmessageを指定してやります。
```python:main
@client.event
async def on_message(message):
    if message.author == client.user:
        return
    if message.content.startswith("$hello"):
        await message.add_reaction("👍")
        emoji_id = 1375023893563310090
        custom_emoji = f"<:emoji_name:{emoji_id}>"
        async with message.channel.typing():
            await asyncio.sleep(5)
        await message.channel.send(content = f"{message.author.mention} Hello world! {custom_emoji}", reference = message)
```

最後に、embedでメッセージを送ったらあと、ユーザーのメッセージをピン留めしましょう。

embedは、いい感じにメッセージを送れるやつです
https://qiita.com/hisuie08/items/5b63924156080694fc81

embed generatorなるものがあります
https://message.style/app/editor

```python:main:main
@client.event
async def on_message(message):
    if message.author == client.user:
        return
    if message.content.startswith("$hello"):
        await message.pin()
        await message.add_reaction("👍")
        emoji_id = 1375023893563310090
        custom_emoji = f"<:emoji_name:{emoji_id}>"
        async with message.channel.typing():
            await asyncio.sleep(5)
        await message.channel.send(content = f"{message.author.mention} Hello world! {custom_emoji}", reference = message)
        embed = discord.Embed(title="Hello World!", description="This is an embedded message.", color=0x00ff00)
        embed.set_author(name=message.author.name)
        embed.set_footer(text="This is a footer.")
        await message.channel.send(embed=embed)
```

だいぶ賑やかなHello Worldになりましたね！

最終的なコード

```python:main
import discord
from dotenv import load_dotenv
import os
import asyncio

load_dotenv()

bot_token: str | None = os.getenv("BOT_TOKEN")
if bot_token is None:
    raise ValueError("BOT_TOKEN environment variable not set")

intents = discord.Intents.default()
intents.message_content = True

client = discord.Client(intents=intents)


@client.event
async def on_message(message):
    if message.author == client.user:
        return
    if message.content.startswith("$hello"):
        await message.pin()
        await message.add_reaction("👍")
        emoji_id = 1375023893563310090
        custom_emoji = f"<:emoji_name:{emoji_id}>"
        async with message.channel.typing():
            await asyncio.sleep(5)
        await message.channel.send(content = f"{message.author.mention} Hello world! {custom_emoji}", reference = message)
        embed = discord.Embed(title="Hello World!", description="This is an embedded message.", color=0x00ff00)
        embed.set_author(name=message.author.name)
        embed.set_footer(text="This is a footer.")
        await message.channel.send(embed=embed)


client.run(bot_token)
```

区切りがいいので、コミットしていきましょう。
```bash
uvx ruff check
```

```bash
uvx ruff format
```

```bash
uv ty check
```

いい感じ
```bash
WARN ty is pre-release software and not ready for production use. Expect to encounter bugs, missing features, and fatal errors.
All checks passed!
```

```
git add .
git commit -m "~~~"
```

# ui系使ってみよう
今回作っていくのは、
/hello で　クリックボタンがあるビュー表示する
/zipcode で 郵便番号検索できるモーダルを開く
コマンド

/ 使ったコマンドをbotに実装していきますが、

```python:main
from discord.ext import commands
```

commandsを使う際に、先ほどまで使っていたclientが邪魔になってしまうので改修していきます
```python:main
import discord
from dotenv import load_dotenv
import os
import asyncio

from discord.ext import commands

load_dotenv()

bot_token: str | None = os.getenv("BOT_TOKEN")
if bot_token is None:
    raise ValueError("BOT_TOKEN environment variable not set")

intents = discord.Intents.default()
intents.message_content = True

bot = commands.Bot(command_prefix="/", intents=intents)

@bot.event
async def on_message(message):
    if message.author == bot.user:
        return
    if message.content.startswith("$hello"):
        await message.pin()
        await message.add_reaction("👍")
        emoji_id = 1375023893563310090
        custom_emoji = f"<:emoji_name:{emoji_id}>"
        async with message.channel.typing():
            await asyncio.sleep(5)
        await message.channel.send(
            content=f"{message.author.mention} Hello world! {custom_emoji}",
            reference=message,
        )
        embed = discord.Embed(
            title="Hello World!",
            description="This is an embedded message.",
            color=0x00FF00,
        )
        embed.set_author(name=message.author.name)
        embed.set_footer(text="This is a footer.")
        await message.channel.send(embed=embed)
    
    await bot.process_commands(message)

bot.run(bot_token)
```

clientの代わりに、botを使っていきます。

## View Classを作る
discord.ui.Viewを継承した、独自のview classを定義していきます。

```python:main
class MyView(discord.ui.View):
    @discord.ui.button(label="Click me!", style=discord.ButtonStyle.primary)
    async def button_callback(self, interaction: discord.Interaction, button: discord.Button):
        await interaction.response.send_message("Button clicked!")
```

定義が終わったら、