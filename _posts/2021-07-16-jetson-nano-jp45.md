---
layout: post
title: Jetson Nanoのセットアップ (JetPack 4.5対応)
categories: tech
author: yasuo ogiuchi
date: 2021-07-16
excerpt: JetPack SDK の新しいバージョンが公開されたタイミングで手元の Jetson Nano の再セットアップをやったので、手順のメモを残しておきます。
---

JetPack SDK の新しいバージョンが公開されたタイミングで手元の Jetson Nano の再セットアップをやったので、手順のメモを残しておきます。

* SSH or RDP を使ってリモートで使う
* Python で機械学習をいろいろやってみる

というあたりが目指す最終形です。
Jetson Nano 初期モデルでしか試していませんが、Jetson Nano 2GB でもほぼ同じ手順で行けるはず。
JetPackのバージョンは4.5前提。4.5より古いと多分ダメ。

[back](/)

## 初期設定

起動するところまでは [公式ドキュメント](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit "Getting Started with Jetson Nano Developer Kit") 通りにセットアップを進めます。特に引っかかることはないと思いますが、言語設定は English にしておけば余計な気を使わなくて済みます。
あと、公式ドキュメントには [ヘッドレスセットアップの方法](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#setup-headless "Initial Setup Headless Mode") についても記載されていますが、うちの macOS Big Sur ではシリアルデバイスが見つからず、ヘッドレスセットアップは試せていません。Big Sur以降はセキュリティの何とかで [USBシリアルが使えないらしい](https://infornography.blue/mac/macos-big-sur-and-kext/ "あまり知られていないmacOS Big Sur以降で利用不可になるkext | INFORNOGRAPHY") です。

## 必要なソフトのインストール

まずはソフトウェアのアップデート。

```bash
sudo apt update
sudo apt upgrade
```

SSH と avahi-daemon

```bash
sudo apt install ssh avahi-daemon
sudo systemctl restart ssh
sudo systemctl restart avahi-daemon
```

ここまでやれば、Jetson Nano のキーボードその他は外しても大丈夫です。
avahi-daemon があれば、初期設定中に設定したマシン名でログインできます (例えば、`ssh 192.168.xxx.xxx` ではなくて、`ssh jetson` みたいな感じ)。

## リモートでの作業

ここから先は、母艦となるPC上での作業。
まずはSSH公開鍵のコピー

```bash
ssh-copy-id -i .ssh/id_rsa.pub <machine-name>
```

ここで ssh でログイン。
ディスプレイマネージャの変更 (以下の手順は、"[Save 1GB of Memory! Use LXDE on your Jetson - JetsonHacks](https://www.jetsonhacks.com/2020/11/07/save-1gb-of-memory-use-lxde-on-your-jetson/)" の内容そのままです)。

```bash
sudo dpkg-reconfigure lightdm
```

ディスプレイマネージャの選択画面が出るので、`lightdm` を選択。
(Jetson Nano 2GB だとデフォルトが lightdm だそうなので、ここのステップは無視してください)

```bash
sudo apt install xrdp
echo lxsession > ~/.xsession
sudo systemctl restart xrdp
```

これでリモートデスクトップが使えます。

## Python関係

pipでのモジュールインストールだとバイナリファイル周りの依存関係がいろいろ面倒くさいので、aptでインストール

```bash
sudo apt install python3-pip python3-numpy python3-pandas python3-matplotlib python3-sklearn python3-ipykernel
```

Tensorflow のインストールは基本的に [公式ドキュメント](https://docs.nvidia.com/deeplearning/frameworks/install-tf-jetson-platform/index.html "Installing TensorFlow For Jetson Platform :: NVIDIA Deep Learning Frameworks Documentation") の手順を踏めばいいのですが、上の流れでPythonモジュールはいろいろインストールしているので、途中段階をすっ飛ばしていきなり

```bash
sudo pip3 install --pre --extra-index-url https://developer.download.nvidia.com/compute/redist/jp/v45 tensorflow
```

でも行けるかもしれません。

## その後

あとはこの辺りの情報をいろいろ

* [Jetson Community Projects / NVIDIA Developer](https://developer.nvidia.com/embedded/community/jetson-projects)
* [Tensorflow　公式サイト](https://www.tensorflow.org/)

[back](/)
