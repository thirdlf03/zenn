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
ただ、メッセージを送るbotだと単純すぎるのでModalやViewを使ったより実践的なBotを作っていきます。

# 対象読者
一応、Python の書き方がわかれば読めるくらいの難易度ですがデコレーターや非同期処理はちょっと難しいかもしれないです。


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
まだpre-release の型チェッカー
型チェッカーで、mypyと比べてかなり高速になっているという噂


## discord.py
pythonのDiscord用のAPIラッパーで、pythonでdiscord bot作るのに使います。


## botの準備
discord botを作っていく前に、botを準備しましょう

まず、開発者ポータルにアクセスしてログインする
https://discord.com/developers/docs/intro

ログインできたら、applicationページに飛ぶ
https://discord.com/developers/applications
![](/images/26/スクリーンショット 2025-05-22 10.48.10.png)


OAuth2のタブに行って、下の方にURL Generatorって項目があるのでbotを選択し、権限は適当なものを選びましょう
![](/images/26/スクリーンショット 2025-05-22 10.54.14.png)


![](/images/26/スクリーンショット 2025-05-22 16.13.16.png)
サーバーにbotを追加できたら、次はbotを動かすためのtokenを取得します。

botにタブに移動して、tokenって項目があるのでそこからtokenコピーしましょう。
もし、reset tokenと書いてあればresetするとtokenが出てきます。

![](/images/26/スクリーンショット 2025-05-22 10.58.30.png)

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
```python:main.py
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

```env:.env
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
```python:main.py
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

![[/images/26/スクリーンショット 2025-05-22 17.00.23.png]]
ざっくり解説すると、

ここで必要なライブラリのインポートとenvからTokenを取得しています
```python:main.py
import discord
from dotenv import load_dotenv
import os

load_dotenv()

bot_token = os.getenv("BOT_TOKEN")
```

botの設定をして、Clientを初期化します。
```python:main.py
intents = discord.Intents.default()
intents.message_content = True

client = discord.Client(intents=intents)
```

botが発火するためのeventを設定していきます。
今回は、メッセージを検知したらイベントが発火してほしいので on_messageを使っています。
```python:main.py
@client.event
async def on_message(message):
```

メッセージ主がclient.user(bot)だったら無視して、もしメッセージの中身が$helloから始まっていたら、そのメッセージが入力されたチャンネルにHello !と返すようになっています。
```python:main.py
    if message.author == client.user:
        return
    if message.content.startswith('$hello'):
        await message.channel.send('Hello world!')
```

client.runでbot起動する
```python:main.py
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

```python:main.py
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
![](/images/26/スクリーンショット 2025-05-22 17.14.37.png)

もしidが出ない場合は、開発者モードにしましょう。
```python:main.py
@client.event
async def on_message(message):
    if message.author == client.user:
        return
    if message.content.startswith("$hello"):
        emoji_id = 1375023893563310090
        custom_emoji = f"<:emoji_name:{emoji_id}>"
        await message.channel.send(f"{message.author.mention} Hello world! {custom_emoji}")
```

![](/images/26/スクリーンショット 2025-05-22 17.18.04.png)

次に、メッセージ入力中と出てくるようにしましょう。
typing()を使うことで入力中にすることができます。デフォルトの秒数が短いので、5秒ほどまってから入力するようにします。

```python:main.py
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

![](/images/26/スクリーンショット 2025-05-22 17.22.04.png)


次に、$helloと入力したユーザーのメッセージにリアクションするようにしてみましょう。
add_reactionでreactionつけることができます。

```python:main.py
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

![](/images/26/スクリーンショット 2025-05-22 17.26.17.png)

次は、ユーザーに返信する形にしてみましょう。
sendのreferenceにmessageを指定してやります。
```python:main.py
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

![](/images/26/スクリーンショット 2025-05-22 17.36.56.png)

最後に、embedでメッセージを送ったらあと、ユーザーのメッセージをピン留めしましょう。

embedは、いい感じにメッセージを送れるやつです
https://qiita.com/hisuie08/items/5b63924156080694fc81

embed generatorなるものがあります
https://message.style/app/editor

```python:main.py:main
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

![](/images/26/スクリーンショット 2025-05-22 17.50.36.png)

最終的なコード

```python:main.py
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
コマンドです。

完成イメージ
![](/images/26/スクリーンショット 2025-05-22 19.07.52.png)
![](/images/26/スクリーンショット 2025-05-22 22.44.50.png)

![](/images/26/スクリーンショット 2025-05-22 19.07.42.png)
/ 使ったコマンドをbotに実装していきますが、

```python:main.py
from discord.ext import commands
```

commandsを使う際に、先ほどまで使っていたclientが邪魔になってしまうので改修していきます
```python:main.py
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

## /helloを実装する
discord.ui.Viewを継承した独自のview classを定義していきます。

```python:main.py
class MyView(discord.ui.View):
    @discord.ui.button(label="Click me!", style=discord.ButtonStyle.primary)
    async def button_callback(self, interaction: discord.Interaction, button: discord.Button):
        await interaction.response.send_message("Button clicked!")
```

定義が終わったら、コマンドを使って呼び出すようにしましょう。

コマンドを反映させるために、on_readyでbot.tree.sync()を呼び出す必要があります。
```python:main.py
@bot.event
async def on_ready():
    await bot.tree.sync()

@bot.tree.command(name="hello", description="Hello world!")
async def hello(interaction: discord.Interaction):
    await interaction.response.send_message("Hello world!", view=MyView())
```

Botを起動して試してみましょう。

もし、/helloと入力してもコマンドが出てこない場合はdiscordを再起動する必要があります。

## /zipcodeを実装する
今回は、zipcloudのapiを呼び出したいので requestsを追加していきます
```bash
uv add requests
```

import
```python:main.py
import requests
```

discord.ui.Modalを継承した独自のmodal classを定義していきます。
```python:main.py
class ZipcodeModal(discord.ui.Modal, title="郵便番号を入力してね"):
    zipcode_input = discord.ui.TextInput(
        label="ハイフン無しの郵便番号を入力してね", 
        style=discord.TextStyle.short,
        placeholder="Type your zipcode...",
        required=True,
        max_length=10
    )
    
    async def on_submit(self, interaction: discord.Interaction):
        address_list = fetch_address(self.zipcode_input.value)
        await interaction.response.send_message(embed=create_embed(address_list))
```

住所を取ってくる関数とembed作る関数を定義する。
```python:main.py
def fetch_address(zipcode: str) -> list[str]:
    url = f"https://zipcloud.ibsnet.co.jp/api/search?zipcode={zipcode}"
    response = requests.get(url)
    address_list = []
    if response.status_code == 200:
        data = response.json()
        if data["results"]:
            for result in data["results"]:
                address = f"{result['address1']}{result['address2']}{result['address3']}"
                address_list.append(address)
        else:
            return "No results found."
    else:
        return "Error fetching data."
    return address_list
    
def create_embed(address_list: list) -> discord.Embed:
    embed = discord.Embed(
        title="Address Search Results",
        description="\n".join(address_list),
        color=0x00FF00,
    )
    embed.set_footer(text="Powered by ZipCloud")
    embed.set_author(name="Address Search Bot")
    return embed
```

modalを呼び出すコマンドを設定
```python:main.py
@bot.tree.command(name="zipcode", description="search address by zipcode")
async def search_address(interaction: discord.Interaction):
    await interaction.response.send_modal(ZipcodeModal())
```

使ってみる


最終的なコード
```python:main.py
import discord
from dotenv import load_dotenv
import os
import asyncio
import requests

from discord.ext import commands

load_dotenv()

bot_token: str | None = os.getenv("BOT_TOKEN")
if bot_token is None:
    raise ValueError("BOT_TOKEN environment variable not set")

intents = discord.Intents.default()
intents.message_content = True

bot = commands.Bot(command_prefix="/", intents=intents)

def fetch_address(zipcode: str) -> list[str]:
    url = f"https://zipcloud.ibsnet.co.jp/api/search?zipcode={zipcode}"
    response = requests.get(url)
    address_list = []
    if response.status_code == 200:
        data = response.json()
        if data["results"]:
            for result in data["results"]:
                address = f"{result['address1']}{result['address2']}{result['address3']}"
                address_list.append(address)
        else:
            return "No results found."
    else:
        return "Error fetching data."
    return address_list
    
def create_embed(address_list: list) -> discord.Embed:
    embed = discord.Embed(
        title="Address Search Results",
        description="\n".join(address_list),
        color=0x00FF00,
    )
    embed.set_footer(text="Powered by ZipCloud")
    embed.set_author(name="Address Search Bot")
    return embed
    

class ZipcodeModal(discord.ui.Modal, title="郵便番号を入力してね"):
    zipcode_input = discord.ui.TextInput(
        label="ハイフン無しの郵便番号を入力してね", 
        style=discord.TextStyle.short,
        placeholder="Type your zipcode...",
        required=True,
        max_length=10
    )
    
    async def on_submit(self, interaction: discord.Interaction):
        address_list = fetch_address(self.zipcode_input.value)
        await interaction.response.send_message(embed=create_embed(address_list))

class MyView(discord.ui.View):
    @discord.ui.button(label="Click me!", style=discord.ButtonStyle.primary)
    async def button_callback(self, interaction: discord.Interaction, button: discord.Button):
        await interaction.response.send_message("Button clicked!")
        
        
        
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
        
@bot.event
async def on_ready():
    await bot.tree.sync()

@bot.tree.command(name="hello", description="Hello world!")
async def hello(interaction: discord.Interaction):
    await interaction.response.send_message("Hello world!", view=MyView())

@bot.tree.command(name="zipcode", description="search address by zipcode")
async def search_address(interaction: discord.Interaction):
    await interaction.response.send_modal(ZipcodeModal())

bot.run(bot_token)

```

一区切りついたので、コミットしていきましょう。

```bash
uvx ruff check
```

```bash
uvx ruff format
```

```bash
uv ty check
```

```bash
❯ uvx ty check                                                                                                                        0s✨   19:30
WARN ty is pre-release software and not ready for production use. Expect to encounter bugs, missing features, and fatal errors.
error[unresolved-import]: Cannot resolve imported module `discord.ext`
 --> main.py:7:6
  |
5 | import requests
6 |
7 | from discord.ext import commands
  |      ^^^^^^^^^^^
8 |
9 | load_dotenv()
  |
info: make sure your Python environment is properly configured: https://github.com/astral-sh/ty/blob/main/docs/README.md#python-environment
info: rule `unresolved-import` is enabled by default

error[invalid-return-type]: Return type does not match returned value
  --> main.py:31:20
   |
29 |                 address_list.append(address)
30 |         else:
31 |             return "No results found."
   |                    ^^^^^^^^^^^^^^^^^^^ expected `list[str]`, found `Literal["No results found."]`
32 |     else:
33 |         return "Error fetching data."
   |
  ::: main.py:20:36
   |
18 | bot = commands.Bot(command_prefix="/", intents=intents)
19 |
20 | def fetch_address(zipcode: str) -> list[str]:
   |                                    --------- Expected `list[str]` because of return type
21 |     url = f"https://zipcloud.ibsnet.co.jp/api/search?zipcode={zipcode}"
22 |     response = requests.get(url)
   |
info: rule `invalid-return-type` is enabled by default

error[invalid-return-type]: Return type does not match returned value
  --> main.py:33:16
   |
31 |             return "No results found."
32 |     else:
33 |         return "Error fetching data."
   |                ^^^^^^^^^^^^^^^^^^^^^^ expected `list[str]`, found `Literal["Error fetching data."]`
34 |     return address_list
   |
  ::: main.py:20:36
   |
18 | bot = commands.Bot(command_prefix="/", intents=intents)
19 |
20 | def fetch_address(zipcode: str) -> list[str]:
   |                                    --------- Expected `list[str]` because of return type
21 |     url = f"https://zipcloud.ibsnet.co.jp/api/search?zipcode={zipcode}"
22 |     response = requests.get(url)
   |
info: rule `invalid-return-type` is enabled by default

Found 3 diagnostics
```

おっと、tyで引っかかってますね。

問題の部分はここで、returnでstrが返ってくる可能性があるのにlist[str]しか返してませんね。

```python:main.py
def fetch_address(zipcode: str) -> list[str]:
    url = f"https://zipcloud.ibsnet.co.jp/api/search?zipcode={zipcode}"
    response = requests.get(url)
    address_list = []
    if response.status_code == 200:
        data = response.json()
        if data["results"]:
            for result in data["results"]:
                address = (
                    f"{result['address1']}{result['address2']}{result['address3']}"
                )
                address_list.append(address)
        else:
            return "No results found."
    else:
        return "Error fetching data."
    return address_list
```

修正したバージョン
```python:main.py
def fetch_address(zipcode: str) -> list[str] | str:
    url = f"https://zipcloud.ibsnet.co.jp/api/search?zipcode={zipcode}"
    response = requests.get(url)
    address_list = []
    if response.status_code == 200:
        data = response.json()
        if data["results"]:
            for result in data["results"]:
                address = (
                    f"{result['address1']}{result['address2']}{result['address3']}"
                )
                address_list.append(address)
        else:
            return "No results found."
    else:
        return "Error fetching data."
    return address_list
```

もう一回、
```bash
uvx ty check
```
を実行すると

```bash
❯ uvx ty check                                                                                                                        0s✨   19:31
WARN ty is pre-release software and not ready for production use. Expect to encounter bugs, missing features, and fatal errors.
error[unresolved-import]: Cannot resolve imported module `discord.ext`
 --> main.py:7:6
  |
5 | import requests
6 |
7 | from discord.ext import commands
  |      ^^^^^^^^^^^
8 |
9 | load_dotenv()
  |
info: make sure your Python environment is properly configured: https://github.com/astral-sh/ty/blob/main/docs/README.md#python-environment
info: rule `unresolved-import` is enabled by default

error[invalid-argument-type]: Argument to function `create_embed` is incorrect
  --> main.py:62:68
   |
60 |     async def on_submit(self, interaction: discord.Interaction):
61 |         address_list = fetch_address(self.zipcode_input.value)
62 |         await interaction.response.send_message(embed=create_embed(address_list))
   |                                                                    ^^^^^^^^^^^^ Expected `list[Unknown]`, found `list[str] | str`
   |
info: Function defined here
  --> main.py:40:5
   |
40 | def create_embed(address_list: list) -> discord.Embed:
   |     ^^^^^^^^^^^^ ------------------ Parameter declared here
41 |     embed = discord.Embed(
42 |         title="Address Search Results",
   |
info: rule `invalid-argument-type` is enabled by default

Found 2 diagnostics
```

またエラーが出ましたね。
今度はcreate_embedで受け取るaddress_listがlist[Unknown]って怒られてますね
直していきましょう。
```python:main.py
    async def on_submit(self, interaction: discord.Interaction):
        address_list = fetch_address(self.zipcode_input.value)
        await interaction.response.send_message(embed=create_embed(address_list))
```

修正したコード
```python:main.py
    async def on_submit(self, interaction: discord.Interaction):
        address_list: list[str] | str  = fetch_address(self.zipcode_input.value)
        if isinstance(address_list, str):
            await interaction.response.send_message(address_list)
            return
        await interaction.response.send_message(embed=create_embed(address_list))
```

もう一度ty check
```
uvx ty check
```

```bash
❯ uvx ty check                                                                                                                        0s✨   19:34
WARN ty is pre-release software and not ready for production use. Expect to encounter bugs, missing features, and fatal errors.
error[unresolved-import]: Cannot resolve imported module `discord.ext`
 --> main.py:7:6
  |
5 | import requests
6 |
7 | from discord.ext import commands
  |      ^^^^^^^^^^^
8 |
9 | load_dotenv()
  |
info: make sure your Python environment is properly configured: https://github.com/astral-sh/ty/blob/main/docs/README.md#python-environment
info: rule `unresolved-import` is enabled by default

Found 1 diagnostic
```
unresolved-importに関しては、動作的にも問題なく動いてて解決策が分からなかったので放置
おそらくtyがまだpre-releaseのためと思われる

# 発展(Cog・Extension) 
discord.pyは確かに便利なんですが、一つのファイルが大きくなりがちです。
それを解決するために、Cogを使っていこうと思います。

## Cogとは
Bot開発においてコマンドやリスナー、いくつかの状態を一つのクラスにまとめてしまいたい場合があるでしょう。コグはそれを実現したものです。

らしいです。

例えば、botに音楽を流す機能とゲーム管理する機能をつけたとして分離したいよねーみたいな時に使えます。

今回は
main.py メイン
hello.py hello系のやつをまとめる
other.py その他
で分割してみようと思います。

```python:main.py
import discord
from discord.ext import commands
from dotenv import load_dotenv
import os
import asyncio


load_dotenv()

bot_token: str | None = os.getenv("BOT_TOKEN")
if bot_token is None:
    raise ValueError("BOT_TOKEN environment variable not set")

intents = discord.Intents.default()
intents.message_content = True

async def main():
    bot = commands.Bot(command_prefix="/", intents=intents)
    
    @bot.event
    async def on_ready():
        print(f'Logged in as {bot.user} (ID: {bot.user.id})')
        print('------')
        await bot.tree.sync(guild=None)

        
    await bot.load_extension("hello")
    await bot.load_extension("other")
    
    await bot.start(bot_token)

if __name__ == "__main__":
    asyncio.run(main())
```

```python:hello.py
import discord
from discord.ext import commands
import asyncio

class MyView(discord.ui.View):
    @discord.ui.button(label="Click me!", style=discord.ButtonStyle.primary)
    async def button_callback(
        self, interaction: discord.Interaction, button: discord.Button
    ):
        await interaction.response.send_message("Button clicked!")

class Hello(commands.Cog):
    def __init__(self, bot):
        self.bot = bot

    @commands.Cog.listener()
    async def on_message(self, message):
        if message.author == self.bot.user:
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

    @commands.hybrid_command(name="hello")
    async def hello(self, ctx):
        await ctx.send("Hello world!", view=MyView())
        
async def setup(bot):
    await bot.add_cog(Hello(bot))
```

```python:other.py
import discord
from discord.ext import commands
import requests

class ZipcodeModal(discord.ui.Modal, title="郵便番号を入力してね"):
    zipcode_input = discord.ui.TextInput(
        label="ハイフン無しの郵便番号を入力してね",
        style=discord.TextStyle.short,
        placeholder="Type your zipcode...",
    )

    async def on_submit(self, interaction: discord.Interaction):
        zipcode = self.zipcode_input.value
        address_list = fetch_address(zipcode)
        if isinstance(address_list, str):
            await interaction.response.send_message(address_list)
        else:
            embed = create_embed(address_list)
            await interaction.response.send_message(embed=embed)
        
def fetch_address(zipcode: str) -> list[str] | str:
    url = f"https://zipcloud.ibsnet.co.jp/api/search?zipcode={zipcode}"
    response = requests.get(url)
    address_list = []
    if response.status_code == 200:
        data = response.json()
        if data["results"]:
            for result in data["results"]:
                address = (
                    f"{result['address1']}{result['address2']}{result['address3']}"
                )
                address_list.append(address)
        else:
            return "No results found."
    else:
        return "Error fetching data."
    return address_list


def create_embed(address_list: list) -> discord.Embed:
    embed = discord.Embed(
        title="Address Search Results",
        description="\n".join(address_list),
        color=0x00FF00,
    )
    embed.set_footer(text="Powered by ZipCloud")
    embed.set_author(name="Address Search Bot")
    return embed

class Other(commands.Cog):
    def __init__(self, bot):
        self.bot = bot
        
    @discord.app_commands.command(name="zipcode", description="search address by zipcode")
    async def zipcode(self, interaction: discord.Interaction):
        await interaction.response.send_modal(ZipcodeModal())
        
async def setup(bot):
    await bot.add_cog(Other(bot))
```

かなりスッキリしましたね。

## Extension (Hot Reload)
実はしれっと使っているんですが、各cogファイルに書いたsetupを呼び出すのに使っています。

```python:main.py
await bot.load_extension("other")
```

今回使っているのは load_extensionですが、reload_extensionにするとbotを再起動せずに変更を試すことができます(hot reload)

めちゃ便利
```python:main.py
await bot.reload_extension("other")
```


参考記事)
https://zenn.dev/nano_sudo/articles/a00db1a55d6c4c


# 終わりに
今回は、uv + ruff + tyを使いつつ割と本格的なdiscord botを作っていきました。
uvやruff format以外はあんまり効果を実感できなかったかもしれませんが、チーム開発やもっと大規模な開発になると役に立つのでぜひ今後のpython開発で使ってみてください。 
