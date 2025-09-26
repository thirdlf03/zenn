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
![](/images/36/serenaadd.png)

みずらいかもしれないですが、Argumentsは , で区切らないといけない

### mcp-config.json
~/.copilot/mcp-config.json でもMCP設定できる
@[gist](https://gist.github.com/thirdlf03/dd0cd0fd7ba23008178e4f16cca64a70)

