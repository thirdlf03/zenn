---
id: 36-zenn-github-copilot-cli
aliases:
tags:
  - github
  - githubcopilot
autonotemover: disable
cssclasses:
  - zenn
date: 2025-09-26
emoji: 🔥
published: true
title: GitHub Copilot CLI入門
type: tech
url: https://zenn.dev/thirdlf/articles/36-zenn-github-copilot-cli
---

# GitHub Copilot CLIがでた
なんとなくTwitter見てたらこんなツイートが
https://x.com/github/status/1971295695853306059

触ってみるしかないってことで、実際に触ってみて入門記事書いてみました

:::message 
これは9/26の情報です
:::

# GitHub Copilot CLIとは？
GitHub CopilotがCLIで使えるようになったものって認識

GitHubのurl
https://github.com/github/copilot-cli

## 対応しているプラットフォーム
- **Linux**
- **macOS**
- **Windows** (experimental)

## 必要なもの
- **Node.js** v22 or higher
- **npm** v10 or higher
- (On Windows) **PowerShell** v6 or higher
- An **active Copilot subscription**. See [Copilot plans](https://github.com/features/copilot/plans?ref_cta=Copilot+plans+signup&ref_loc=install-copilot-cli&ref_page=docs).

ここで注意なのが、以下のいずれかのプランじゃないと使えない
- GitHub Copilot Pro
- GitHub Copilot Pro+
- GitHub Copilot Business
- GitHub Copilot Enterprise plans

## インストール
いつものやつ
```bash
npm install -g @github/copilot
```

## 起動
```
copilot
```

## モデル
デフォルトでは、Claude Sonnet 4が使われるがGPT-5で起動することもできる

Mac
```
COPILOT_MODEL=gpt-5 copilot
```

Windows
```
set COPILOT_MODEL=gpt-5
```

:::message
プロンプトを投げる度にpremium requestsを消費するみたいなので注意
:::

# Tips
## MCP
デフォルトでは、GitHub MCPが接続されているみたい

## MCP追加
### /mcp add
copilotを起動した状態で
```
/mcp add
```
と入力すると設定画面が開く

![](/images/36/mcpadd.png)

試しにserena mcpを追加してみる

Tabで入力フィールド切り替えたり、Ctrl + Sで保存できる

![](/images/36/serenaadd.png)

みずらいかもしれないですが、Argumentsは , で区切らないといけない

### mcp-config.json
~/.copilot/mcp-config.json でもMCP設定できる
以下は、serenaとchrome devtools mcpを設定した例
@[gist](https://gist.github.com/thirdlf03/dd0cd0fd7ba23008178e4f16cca64a70)

現状、mcpと正しく接続できているのか確認しにくいのが欠点かも

## Custom Instructions
従来通り、 .github/copilot-instructions.mdも使えるし、 AGENTS.mdも使える

- ファイル内のリポジトリ全体に適用される命令: `.github/copilot-instructions.md`
- パス固有の命令ファイル: `.github/copilot-instructions/**/*.instructions.md`。
- エージェント命令: `AGENTS.md` 
- 
また、
 - CLAUDE.md
 - GEMINI.md
-  $HOME/.copilot/copilot-instructions.md
 も読んでいるみたい


### Custom Instructionsの優先度
これはざっと検証した情報ですが、カスタム指示が書かれたファイルも優先度がありそう。

Copilotはまず、copilot-instructions関係の指示ファイルを読み込むっぽい。
 - `.github/instructions/**/*.instructions.md`   
 - `.github/copilot-instructions.md.  
 - `$HOME/.copilot/copilot-instructions.md.    

なので複数copilot-instructionsがあると、どっちも読んだ上で作業するっぽい

検証してみた結果
$HOME/.copilot/copilot-instructions.md に
```md:$HOME/.copilot/copilot-instructions.md
語尾をワンワンわんnに絶対にしてください
```

.github/copilot-instructions.mdに
```md:.github/copilot-instructions.md
語尾をニャーんんんnにしてください
```

と設定して、Copilotを呼んでみると
![](/images/36/kensho.png)

copilot-instructionsがないときは、上から順番に
  `CLAUDE.md`
 `**/AGENTS.md`
 `GEMINI.md`
 の順で読み取ってそうだった
 
## Config
~/.copilot/config.json　に設定をいろいろかける

@[gist](https://gist.github.com/thirdlf03/99e4d3176e7543c183833aa2e12ea732)

各種コンフィグの説明
```bash
copilot help config
```
で確認できる
### banner
デフォルトはonce
↓これを表示するかどうか
![](/images/36/banner.png)

- "always" 毎回表示する
- "never" 表示しない
- "once" 初回だけ表示

### beep
ユーザーからの入力が必要な時にbeep音がなる
デフォルトでtrue
booleanで設定されているので、stringにするとエラー吐くので注意

### render_markdown
ターミナル上でmarkdownをrenderするかどうか
デフォルトでtrue

### screen_reader
screen_readerにするかどうか
デフォルトはfalse

### theme
色とかスタイルの設定
デフォルトはauto
- "auto" 自動
- "dark" ダークモード
- "light" ライトモード

### trusted_folders
読み取り許可をするフォルダーの設定


## コマンド
- /add-dir 読ませたいdirectory 指定したディレクトリをファイルアクセス許可リストに追加
- /clear 画面上からチャット履歴を削除
- /cwd directory(optional)  ディレクトリを選択しないと現在のディレクトリ表示、ディレクトリを指定すると作業ディレクトリの変更 
- /exit CLIから抜ける
- /feedback  CLIに関するfeedbackを送れる
- /help ヘルプ表示

いろいろ表示される
![](/images/36/help.png)
- /list-dirs ファイルアクセスが許可されたディレクトリ一覧を表示
- /login Copilotにログイン
- /logout Copilotからログアウト
- /mcp show|add|edit|delete|disable|enable server-name
MCPに関するいろんなことができる
- /reset-allowed-tools 許可したツールのリセット
- /session CLIのセッション表示 IDが表示されるだけ
- /theme show|set|list auto|dark|light  テーマ関係
- /user show|list|switch  Manage GitHub user listらしいです

## 使えるオプション

Usage: copilot options command

  - --add-dir directory          ファイルアクセス許可リストにディレクトリを追加（複数回使用可能）
 -  --allow-all-tools                確認なしで全てのツールを自動的に実行することを許可。非対話モードで必要（環境変数: COPILOT_ALLOW_ALLでも設定可能）
  - --allow-tool tools...          特定のツールを許可します
 -  --banner                         起動時に常にアニメーションバナーを表示
 -  --deny-tool tools...]          特定のツールを拒否。--allow-tool や --allow-all-tools よりも優先される
  - --disable-mcp-server server-name  特定のMCPサーバーを無効にする（複数回使用可能）
  - -h, --help                       コマンドのヘルプを表示
  - --log-dir directory           ログファイルのディレクトリを設定（デフォルト: ~/.copilot/logs/）
  - --log-level level              ログレベルを設定 (error, warning, info, debug, all, default, none)
 -  --no-color                       全てのカラー出力を無効にする
  - -p, --prompt text              対話モードなしでプロンプトを直接実行
  - --resume sessionId           前のセッションから再開します（オプションでセッションIDを指定可能）
  - --screen-reader                  スクリーンリーダーの最適化を有効にします
  - -v, --version                    バージョン情報を表示します

# GitHub Copilot CLIをガッツリ使ってみよう

著者の環境
- OS: Mac
- モデル: GPT-5 (COPILOT_MODEL=gpt-5 copilotで起動)
- MCP: serena、context7、chrome-devtools, fetch mcp
使ったmcp-config.jsonです。 context7のapikeyは自分のやつを設定してください。
@[gist](https://gist.github.com/thirdlf03/646250bf509665877c27534c2df170a9)

- copilot-instructions.md はこれを参考に改変したものを使用

https://qiita.com/ulxsth/items/b2e6f366e3f87ca2ae1c


```md:.github/copilot-instructions.md
# 基本
- 日本語で応答すること
- 必要に応じて、ユーザに質問を行い、要求を明確にすること
- 作業後、作業内容とユーザが次に取れる行動を説明すること
- 作業項目が多い場合は、段階に区切り、git commit を行いながら進めること。その際、GitHub MCPを使用してください
  - semantic commit を使用する
- コマンドの出力が確認できない場合、 get last command / check background terminal を使用して確認すること

## develop
各作業を以下のように定義する。
- 「調査」と指示された場合、都度 docs/reports に記載すること
- 「計画」と指示した場合、docs/tasks.md に計画を記載する
  - 前回の内容が残っている場合は、読まずに消して構わない
  - コードベース / docs を読み込み、要件に関連性のあるファイルパスをすべて記載すること
  - 不明な点については、fetch mcp、context7 mcpを使用して検索すること
  - 必要最小限の要件のみを記載すること
  - このフェーズで、コードを書いては絶対にいけない
- ユーザが「実装」と指示した場合、docs/tasks.md に記載された内容に基づいて実装を行う
  - 記載されている以上の実装を絶対に行わない
  - ここでデバッグしない
- 「デバッグ」と指示された場合、直前のタスクのデバッグ「手順」のみを示す

## documents
- docs/reports/*.md : 調査レポート
- docs/schema.md : データ構造
- docs/requirements.md : 要件定義
```


## ToDoアプリ作ってみる
もはややらなくてもいい気がしますが、やってみます

与えたプロンプト
```
reactでToDoアプリを作りたいです。計画を立てて
```

![](/images/36/response1.png)
与えたプロンプト2
```
todo-appで良いです。 TS使用で大丈夫です。 cssはtailwind使って、テストはMVP外で大丈夫です。
実装を始める前にgit initして問題ありません
```


できたもの

デプロイ先
https://github-copilot-cli-todo-app.pages.dev/

リポジトリ
https://github.com/thirdlf03/github-copilot-cli-todo-app



# 感想
入門記事書くためにざっと触ってみたが、感触は悪くなかったです。
ただ、細かいツールの呼び出し方で怪しいところがあったので、何やってるか監視しといた方がいいかもしれないです。
https://x.com/thirdlf1/status/1971416655139045831

 GitHub Copilotに慣れていて、CLI系(Claude CodeやCodex)を触ったことない方や copilot-instructionsなど資源がある方はぜひ触ってみてください！！

