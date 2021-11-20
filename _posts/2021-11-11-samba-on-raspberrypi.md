---
layout: post
date: 2021-11-20 18:00
author: おぎうちやすお
title: Raspberry PiでSambaサーバー
excerpt: Raspberry PiをSambaサーバーとして動かせるようにするまで。
---

Raspberry PiをSambaサーバーとして動かせるようにするまで。

## OSイメージをmicroSDに書き込み

ここはMac/Windowsでの作業。

1. "[Raspberry Pi OS – Raspberry Pi](https://www.raspberrypi.com/software/)" から Raspberry Pi Imagerをダウンロードしてインストール
2. 「システム環境設定」 > 「セキュリティとプライバシー」でRaspberry Pi Imagerにフルディスクアクセス権限を付与
3. microSDをMacに差し込む。OSだけのインストールなら8GBぐらいでOK。
4. Raspberry Pi Imagerを立ち上げて、Operating Systemに"Raspberry Pi OS Lite"、Storageで先ほど差し込んだmicroSDを選択し、"WRITE"で書き込み開始。

……が公式手順。

ただ、年に1回あるかないかのRaspberry Piの起動ディスク作成のためだけにわざわざ専用ソフトをインストールするのもアホらしいので、"[Operating system images – Raspberry Pi](https://www.raspberrypi.com/software/operating-systems/)"から適当なOSイメージをダウンロードして、[balenaEtcher](https://www.balena.io/etcher/)か`dd`コマンドあたりで書き込む方がいいかもしれない。

書き込みが終わってもMac/Windows上での作業が続くので、microSDはそのまま。

## ヘッドレス設定のための仕込み

引き続きMac/Windowsでの作業。

### sshの起動設定

microSDのルート直下に`ssh`という名前の空のファイルを作成すればsshサーバーが起動する。デフォルトで起動しているわけではないので注意 (ファイルじゃなくて`ssh`という名前の空の**フォルダ**でもいけたので、Windowsマシンだとそれでいいかも)。

```sh
$ touch ssh
```
起動したらファイルは自動で削除される模様。

ヘッドレスじゃなくてもよければ、`raspi-conf`コマンドで設定。

同じくルート直下に以下の内容の`wpa_supplicant.conf`というファイルを作成。

```ini
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=JP

network={
    ssid="<SSID名>"
    psk="<パスワード>"
}
```

## OSの設定

ここからはRaspberry Piでの作業。

microSDをRaspberry Piに差し込んで電源を入れ、完全に起動するまでしばらく待つ。

これで、

- ホスト名 `raspberrypi.local`
- ユーザー名 `pi`
- パスワード `raspberry`

でアクセスできるようになる。

アクセスできたら、最初にパスワード変更。

```sh
$ passwd
```

ホスト名変更、時間帯変更、その他。

```sh
$ raspi-config
```
ソフトウェア更新。

```sh
$ sudo apt update
$ sudo apt upgrade
```

Sambaインストール。

```sh
$ sudo apt install samba
```

## smb.confの編集

`nano /etc/samba/smb.conf`でsmb.confの編集。(エディタは`vi`でもいいけど)

末尾に以下を追記。

```ini
[pi]
  comment = Welcome
  path = /XXXXXXXX
  public = no
  read only = no
  browsable = yes
  force user = pi
```

`path = /XXXXXXXX`は自分の環境に合わせて適宜書き換え。

Sambaサーバーを再起動。

```sh
sudo systemctl restart smbd
```

Sambaのユーザーパスワードを設定してSambaサーバーを再起動  
(Sambaのユーザーとマシンのユーザーは別物なので、セキュリティ的にはパスワードも別にした方がいい)

```sh
sudo smbpasswd -a pi
sudo systemctl restart smbd
```

## 動作確認

`sudo pdbedit -L`で出力に`pi:1000`というのがあれば設定完了。