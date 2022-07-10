---
layout: post
date: 2022-07-10 14:50
author: おぎうちやすお
title: ログアウト状態でも外付けストレージはマウント
excerpt: Macでユーザーがログアウトしていても外付けストレージをマウントしたままにしておく方法
categories: categories
---

Macの場合、ローカルユーザーがログアウトすると外付けストレージのマウントが自動的に解除される。
このため、ログアウトした状態だと、リモートからログインしても外付けストレージにアクセスできないし、Time Machineによるバックアップもできないことになる。

ということで、以下のコマンドでローカルユーザーがログインしていなくてもストレージはマウントされたままにしておくと便利 (下のコマンドは横に長いので注意)。

```sh
$ sudo defaults write /Library/Preferences/SystemConfiguration/autodiskmount AutomountDisksWithoutUserLogin -bool yes
```

無効にするときは、次のコマンドで。

```sh
$ sudo defaults delete /Library/Preferences/SystemConfiguration/autodiskmount AutomountDisksWithoutUserLogin
```

参考 [Configure OS X to mount external drives at boot · GitHub](https://gist.github.com/jabenninghoff/f13f81c286e914c48942f973f5d15819)