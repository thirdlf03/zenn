---
id: 36-zenn-github-copilot-cli
aliases:
tags:
  - cloudflare
autonotemover: disable
cssclasses:
  - zenn
date: 2025-09-26
emoji: 🔥
published: false
title: GitHub Copilot CLI使ってみた
type: tech
url: https://zenn.dev/thirdlf/articles/36-zenn-github-copilot-cli
---

# GitHub Copilot CLIがでた
なんとなくTwitter見てたらこんなツイートが
https://x.com/github/status/1971295695853306059

CLI系大好きな人間としては触ってみるしかないと思ったので、実際に触ってみてClaude CodeやCodex、Amazon Q Developerと比較していきます

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
デフォルトでは、Claude Sonnet 4が使われるがGPT-5で起動することもできるらしい

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
 `.github/instructions/**/*.instructions.md` `.github/copilot-instructions.md`
  `$HOME/.copilot/copilot-instructions.md

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
注意すべき時に音がなるらしい
デフォルトでtrue
booleanで設定されているので、stringにするとエラー吐くので注意

## render_markdown
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

# GitHub Copilot 



