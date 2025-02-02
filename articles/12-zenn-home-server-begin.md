---
published: false
cssclasses:
  - zenn
type: tech
emoji: 💻
title: ラズパイで自宅サーバー入門してみた
topics: 
date: 2025-02-02
AutoNoteMover: disable
url: https://zenn.dev/thirdlf/articles/12-zenn-home-server-begin
tags: []
aliases:
---
# きっかけ
ラズパイたくさん持ってるお友達からRaspberry Pi 4もらったのをきっかけに、環境を整えていきました。

環境構築してる時、プログラム作ってる時とはまた違った面白さがあってこれがインフラエンジニアの気持ちか.ってなりました。

# サーバー周りの環境
配線は汚いです ; w ;
![](/images/article-12/outi.webp)
パナソニックのFA-ML24TCPoE+からPoEでラズパイを起動してます。
ELECOMのセールで買ったAPを動かすために、PoE給電できるスイッチングハブを用意していたのでちょうどよかったです。

スイッチングハブの初期設定をする際に、コンソール接続する必要があって、シリアルポートで接続だるいなーって思ってたら
![](/images/article-12/teraterm.webp)

まさかのTera Termくんが対応してて、めちゃ助かりました。
手元に、windows serverの環境がなかったのでまじで神です。

# 環境設定
## Wireguard (PiVPN)
VPNサーバーとして動いて欲しかったので、Wireguardを導入しています。
PiVPN使うと、超お手軽にセットアップできるのでぜひ使ってみてください！
ルーターのポート解放忘れがちなので、注意です

## Proxmox
オープンソースの仮想化プラットフォーム。
まだ、入門したばっかで詳しくはしらないんですが、管理画面を眺めてるだけ楽しいです

VMを簡単に作れるのと、リソースの管理がやりやすくて便利。
おすすめです

## k3s (wip)


# 詰まったポイント
上記の環境を作ってる上で詰まったポイントを書いておきます。

## 名前解決できない
DNSサーバー設定していなかったため起こってた
/etc/resolv.conf
に書いてあげるとok

## sshできない
普通にユーザーの名前間違えてました。
タイポ注意
## pimox
当初、pimoxを使おうとしていたんですが、
pimoxを使うには、Debian bullseyeである必要があったみたいで、動かなかったので普通にProxmox 8系を使いました。

## Wireguardのホストと繋がらない
ルーターのポートを開けるのを忘れてて繋がっていませんでした。

## Proxmoxで、ubuntuブート失敗
Ubuntu server 22.04をセットアップしてて、設定が終了しリブートしたタイミングでブート失敗するようになっていました。
原因は、EFI Deskが設定されていなかったせいでした。
proxmoxの管理画面から、ubuntu serverのVMを選択しHardwareの欄でEFI Deskを追加すると直りました。






