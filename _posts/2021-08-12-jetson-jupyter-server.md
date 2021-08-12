---
layout: post
date: 2021-08-12 21:54
author: おぎうちやすお
title: Jetson NanoでJupyter Server
excerpt: Jetson Nano上で機械学習関連の環境整備して、Jupyter Server動かして、PCからいろいろできるようにしてみよう、というお話。
---

Jetson Nano上で機械学習関連の環境整備して、Jupyter Server動かして、PCからいろいろできるようにしてみよう、というお話。

環境整備に手間かけたくないので、Dockerを使います。

## 前準備

Jetson Nanoの準備については、公式の "[Getting Started With Jetson Nano Developer Kit : NVIDIA Developer](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit)" とか、[このサイト上のポスト]({% post_url 2021-07-16-jetson-nano-jp45 %})あたりを見てください。

## Docker　コンテナの導入〜起動

だいたい "[Catalog : NVIDIA NGC](https://ngc.nvidia.com/catalog)" にある "[NVIDIA L4T ML : NVIDIA NGC](https://ngc.nvidia.com/catalog/containers/nvidia:l4t-ml)" の記述通り。

(初回起動時のみ) Jetsonのターミナルで以下のコマンドを入力してコンテナをpull。

```sh
sudo docker pull nvcr.io/nvidia/l4t-ml:r32.6.1-py3
```

以下のコマンドでコンテナを起動。例によって初回は起動まで時間がかかります。

```sh
sudo docker run -it --rm --runtime nvidia --network host nvcr.io/nvidia/l4t-ml:r32.6.1-py3
```

これでJupyter Labが起動します。

## Jupyter Serverの利用

### ブラウザから

同じLAN上の別のPCからJetson Nanoの8888番ポート (要するに `http://<machine name>:8888`) にブラウザでアクセスするとJupyter Labのパスワード認証画面に入れます。

Jetsonのターミナルの指示通り"nvidia"というパスワードで入れば、Jupyter Labの画面が開いてPytorchとかTensorFlowとか使えるようになります。

これが多分基本的な使い方。

### VSCodeからのアクセス

ブラウザからのアクセスだとコマンド補完が効かずあまり使い物にならないので、以下VSCodeからアクセスする方法です。動作確認で使ったVSCodeのバージョンは1.59.0。

1. 既存の or 新規作成したJupyter Notebookを開いた状態にしておく
2. コマンドパレットから "Jupyter: Specify local or remote Jupyter server for connection" を選択
3. "Existing" を選択
4. Jupyter Serverのアドレス (例えば、`http://<machine name>:8888`) を入力
5. 「httpsじゃないアクセスでtoken使わなかったらセキュリティ上の問題がどうこう」(意訳)というアラートが出るが、構わずYesを選択
6. パスワード入力が求められるので、"nvidia"と入力

これでVSNoteからJetson Nano上で動作するJupyter Serverが使えるようになります。

## その他

`docker`コマンドで`sudo`が嫌、という方は「[Dockerコマンドをsudoなしで実行する方法 - Qiita](https://qiita.com/DQNEO/items/da5df074c48b012152ee)」あたりを参考にどうぞ。
