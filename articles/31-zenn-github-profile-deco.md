---
id: 31-zenn-github-profile-deco
aliases: 
tags: 
autonotemover: disable
cssclasses:
  - zenn
date: 2025-07-17
emoji: 
published: false
title: GitHubのプロフィールをデコろう
type: idea
url: https://zenn.dev/thirdlf/articles/31-zenn-github-profile-deco.md
---

# はじめに
まず、リポジトリの準備から始めましょう！

![](/images/31/createrepo.png)


リポジトリの名前を自分のユーザー名にします。
![](/images/31/myrepo.png)
そうすると、special repositoryと出ると思います。


add README.mdにチェックを入れて、リポジトリを作成しましょう！
![](/images/31/addreadme.png)

リポジトリ作成できたら、早速README.mdをいじっていきます。
編集ボタンを押します
![](/images/31/edit.png)

ここからは、デコるための素材集めができるサイト紹介です。
# Icon系

## Skill Icons
よくみるやつ
https://skillicons.dev/

100以上の技術アイコンをURLパラメータで指定するだけで表示。テーマやレイアウトもカスタマイズ可能。高速で統一されたデザインが特徴。

```markdown
[![My Skills](https://skillicons.dev/icons?i=js,html,css,react,nodejs)](https://skillicons.dev)
```

## Devicon
https://devicon.dev/

150以上の技術アイコンを提供するオープンソースライブラリ。Font形式とSVG形式の両方に対応。CDN経由またはNPMでの利用が可能。

```html
<i class="devicon-javascript-plain colored"></i>
```

## TechStack Generator
https://techstack-generator.vercel.app/

アニメーション付きの技術スタックアイコンを生成。30以上の技術に対応し、動的なSVGアイコンを生成。GitHubのREADMEでの使用に最適化。

## Animated Fluent Emoji
https://animated-fluent-emoji.vercel.app/

Microsoftの動くFluent絵文字を集めたサービス。カテゴリー別に整理され、開発プロジェクトに簡単に統合可能。

## Profile Technology Icons
https://marwin1991.github.io/profile-technology-icons/

オンラインジェネレーターで技術アイコンを生成。アイコンサイズのカスタマイズが可能で、Markdownコードを自動生成。


# Badges / Shield

## Shields.io
https://shields.io/

月間16億枚以上配信される高性能バッジサービス。動的・静的両方のバッジを生成可能。数百のサービスと連携し、豊富なカスタマイズオプションを提供。

```markdown
![build:passing](https://img.shields.io/badge/build-passing-brightgreen)
```

## Badges4-README.md-Profile
https://github.com/alexandresanlim/Badges4-README.md-Profile

30以上のカテゴリにわたる大量のバッジコレクション。「for-the-badge」スタイルで統一されたデザイン。コピー&ペーストで簡単に使用可能。

## Markdown Badges
https://ileriayo.github.io/markdown-badges/

AI、ブロックチェーン、データベース、フレームワークなど幅広い分野をカバー。複数のスタイルオプション（flat、plastic、social）を提供。

# Stats

## GitHub Readme Stats
https://github.com/anuraghazra/github-readme-stats

GitHub統計情報を動的に生成するサービス。統計カード、使用言語カード、リポジトリピン、WakaTime連携など豊富な機能。30以上のテーマと多言語サポート。

```markdown
![GitHub Stats](https://github-readme-stats.vercel.app/api?username=yourusername)
```

## GitHub Profile Trophy
https://github.com/ryo-ma/github-profile-trophy

GitHub統計をトロフィー形式で表示。SSS〜Cまでのランキングシステム、30種類以上のテーマ、リアルタイム更新機能。

```markdown
[![trophy](https://github-profile-trophy.vercel.app/?username=yourusername)](https://github.com/ryo-ma/github-profile-trophy)
```


# 草系・アクティビティ可視化

## Generate Snake Game from GitHub Contribution Grid
https://github.com/marketplace/actions/generate-snake-game-from-github-contribution-grid

コントリビューショングラフからアニメーションのスネークゲームを生成。GIF・SVG形式での出力、複数のカラーパレット、毎日自動更新機能。

```yaml
- uses: Platane/snk@v3
  with:
    github_user_name: ${{ github.repository_owner }}
    outputs: |
      dist/github-snake.svg
      dist/github-snake-dark.svg?palette=github-dark
```

## GitHub Profile 3D Contrib
https://github.com/yoshi389111/github-profile-3d-contrib

GitHub Actionを使用して3D立体的な貢献カレンダーを生成。毎日自動更新され、パブリック・プライベート両方のリポジトリをサポート。

## GitHub Readme Activity Graph
https://github.com/Ashutosh00710/github-readme-activity-graph

過去31日間のGitHub活動を動的に生成するアクティビティグラフ。Vercelでセルフホスト可能、DRACULAなど様々なテーマをサポート。

```markdown
[![GitHub Activity Graph](https://github-readme-activity-graph.cyclic.app/graph?username=yourusername&theme=dracula)](https://github.com/yourusername)
```

## GitHub Isometric Contributions
https://github.com/jasonlong/isometric-contributions

ブラウザ拡張機能で貢献グラフを等角投影（アイソメトリック）のピクセルアート版で表示。Chrome・Firefox両方に対応。

## GitHub Skyline
**注意**: 元のサービス (https://skyline.github.com/) は2023年に廃止されました。

**代替サービス**:
- GitHub Skyline CLI Extension: https://github.com/github/gh-skyline  
- Git Skyline (3rd party): https://gitskyline.vercel.app/

貢献グラフを3Dの都市景観として表示。アニメーション付きで、STLファイルとして3Dプリント可能。

## GitHub City
https://honzaap.github.io/GithubCity/

GitHub貢献から3D都市を生成。Three.jsを使用して開発され、インタラクティブな3D可視化を提供。

# 音楽・エンターテイメント

## Spotify GitHub Profile
https://github.com/kittinan/spotify-github-profile

現在再生中のSpotify楽曲をGitHubプロフィールに表示。リアルタイム更新でSpotify APIと連携。

```markdown
[![Spotify](https://spotify-github-profile.kittinanx.com/api/spotify?background_color=0d1117&border_color=ffffff)](https://spotify-github-profile.kittinanx.com/api/spotify)
```

## Spotify Readme
https://github.com/tthn0/Spotify-Readme

動的でカスタマイズ可能なSpotify現在再生中ウィジェット。再生していない時は最近の楽曲を表示。

## Spotify Recently Played README
https://github.com/JeffreyCA/spotify-recently-played-readme

最近再生したSpotifyトラックをGitHubプロフィールREADMEに表示。Last.fmとの連携も可能。

## Readme Spotify Now Playing
https://github.com/Om-Thorat/Readme-Spotify-Now-Playing

SVG形式のSpotify現在再生中ウィジェット。READMEやWebサイトのどこでも使用可能。

# 総合ツール・ジェネレーター

## GitHub Profile README Generator
https://rahuldkjain.github.io/gh-profile-readme-generator/

カスタマイズされたGitHubプロフィールREADMEを生成。スキル・技術、ソーシャルリンク、訪問者カウンター、統計カードなど豊富な要素。チェックボックスベースの直感的インターフェース。

## Awesome GitHub Profile README
https://github.com/abhisheknaiidu/awesome-github-profile-readme

優れたGitHubプロフィールREADMEのキュレーションリスト。リアルタイム更新される最新事例を収集。

## GitHub Readme Collection
https://github.com/madushadhanushka/github-readme

GitHub READMEウィジェットのコレクション。多様なウィジェットとツールを一箇所で管理。

# その他の便利ツール

## 訪問者カウンター
```markdown
![Profile views](https://komarev.com/ghpvc/?username=yourusername&label=Profile%20views&color=0e75b6&style=flat)
```

## GitClear Activity Widget
https://www.gitclear.com/github_profile_dynamic_readme_free

無料で動的なGitHubプロフィール用チェンジログジェネレーター。時間別更新で最近の作業を視覚的に表示。GitHub、GitLab、BitBucket、Azure DevOpsに対応。

# アニメーション・エフェクト

## Readme Typing SVG
https://github.com/DenverCoder1/readme-typing-svg

動的に生成されるタイピングエフェクトSVG。テキストを入力・削除するアニメーションでプロフィールを魅力的に演出。

```markdown
[![Typing SVG](https://readme-typing-svg.demolab.com/?lines=First+line+of+text;Second+line+of+text)](https://git.io/typing-svg)
```

## GitHub Profile Header Generator
https://github.com/leviarista/github-profile-header-generator

カスタムヘッダー画像を生成。個人情報やスキルを含むプロフェッショナルなプロフィールヘッダー作成。

# 日常・ライフスタイル

## GitHub Readme Daily Quotes
https://github.com/cheehwatang/github-readme-daily-quotes

毎日更新される名言・格言ウィジェット。light、dark、vue、algolia、nord、dracula、monokaiなど複数テーマを提供。

```markdown
![Github Readme Daily Quotes](https://readme-daily-quotes.vercel.app/api)
```

## Weather Readme Widget
https://github.com/saumiko/weather-readme-widget

OpenWeatherMap APIを使用した天気情報ウィジェット。現在の天気とアイコンを動的に表示。

## Current Book Status from GoodReads
https://github.com/theFr1nge/goodreads-readme

GoodReadsと連携して現在読んでいる本の情報を表示。読書進捗を自動同期。

# ソーシャルメディア・コミュニケーション

## Discord Status Integration
https://github.com/cnrad/lanyard-profile-readme

Lanyard APIを使用してDiscordのリアルタイムステータスを表示。現在のアクティビティや使用中のアプリケーションを表示。

```markdown
[![Discord Presence](https://lanyard.cnrad.dev/api/:id)](https://discord.com/users/:id)
```

## Discord README Badge
https://github.com/Zyplos/discord-readme-badge

IDEのRich Presenceと連携したDiscordバッジ。現在作業中のプロジェクトを表示。

```markdown
![My Discord](https://discord-readme-badge.vercel.app/api?id=<your discord id>)
```

## GitHub Readme Medium
https://github.com/omidnikrah/github-readme-medium

Medium記事を自動表示。GitHub Actionsを使用してRSSフィードから最新記事を取得。

## GitHub Readme Twitter
https://github.com/gazf/github-readme-twitter

最新ツイートをプロフィールに表示。リアルタイムでTwitter活動を反映。

**注意**: Twitter APIの有料化により、将来的に機能制限される可能性があります。

# ブログ・コンテンツ

## Blog Post Workflow
https://github.com/gautamkrishnar/blog-post-workflow

dev.to、Medium、個人ブログなどからRSSフィードを取得して最新記事を表示。GitHub Actions使用。

```yaml
- uses: gautamkrishnar/blog-post-workflow@master
  with:
    max_post_count: "4"
    feed_list: "https://dev.to/feed/username"
```

## GitHub Readme StackOverflow
https://github.com/omidnikrah/github-readme-stackoverflow

StackOverflowのアクティビティと評価を表示。質問・回答履歴を可視化。

# 学習・スキル

## LeetCode Stats
https://github.com/KnlnKS/leetcode-stats

LeetCode問題解決統計を表示。解決した問題数、難易度別統計、ランキングを表示。

## Codewars Badge
https://www.codewars.com/users/username/badges

Codewarsのランクと達成度を表示するバッジ。プログラミングスキルの証明。

```markdown
![Codewars](https://www.codewars.com/users/username/badges/large)
```

## WakaTime Stats
https://github.com/anmol098/waka-readme-stats

WakaTimeと連携したコーディング時間統計。言語別・プロジェクト別の開発時間を表示。

# エンターテイメント

## README Jokes
https://github.com/ABSphreak/readme-jokes

毎日更新される開発者向けジョークを表示。プロフィールにユーモアを追加。

```markdown
![Jokes Card](https://readme-jokes.vercel.app/api)
```

## Random Dev Memes
https://github.com/techytushar/random-memes-for-github-readme

ランダムな開発者向けミームを表示。プロフィールを楽しく演出。

## GitHub Profile Views Counter
https://github.com/arturssmirnovs/github-profile-views-counter

プロフィール閲覧数カウンター。訪問者数を追跡・表示。

```markdown
![Profile views](https://komarev.com/ghpvc/?username=yourusername&label=Profile%20views&color=0e75b6&style=flat)
```