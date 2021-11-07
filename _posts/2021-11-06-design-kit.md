---
layout: post
date: 2021-11-06 22:32
author: おぎうちやすお
title: はがきデザインキットの住所録書き出し
excerpt: 郵便局が配布していた年賀状 (だけじゃないけど) 作成ソフト「はがきデザインキット」の住所録データを書き出してみよう、というお話。
---

郵便局が配布していた年賀状 (だけじゃないけど) 作成ソフト「はがきデザインキット」の住所録データを書き出してみよう、というお話。

Mac の場合、はがきデザインキットの住所録の本体は `~/Library/Preferences/designKit.<英数字の列>/Local Store/yubin10.db`。  
(Windows だとファイルがあるのは `~\AppData\Roaming\designKit.<英数字の列>\Local Store` らしいです)

まずターミナルで `sqlite3`　を使って `yubin10.db` を開きます。

```sh
$ sqlite3 yubin10.db
SQLite version 3.36.0 2021-06-18 18:58:49
Enter ".help" for usage hints.
```

sqlite3 のコマンドで中身を確認します。Mac なら `sqlite3` は `/usr/bin/sqlite3` としてデフォルトで入っています。(WindowsだったらWSLあたりを使うのが今風かな?)

```sql
sqlite> .tables
post_app_address_category_mst    post_app_adressbook
post_app_address_category_tbl    post_app_bookmark_tbl
post_app_address_history_tbl     post_app_config
post_app_addresslayout           post_app_renmei
post_app_addresslayout_template
```

`post_app_adressbook` というテーブルがあるのを確認。

``` sql
sqlite> select * from post_app_adressbook
```
で住所録データがぞろぞろと画面出力されるので、あとはエディタにコピペするなりすればOK。  
また、連名のデータは住所録データとは別に `post_app_renmei` に入っています。

出力内容にはちょっと癖があって、項目間の区切りは `,` ではなく、`|`　だったり、各行の後ろの方によく分からない文字列があったり (たぶん、前年までの年賀状の送受のデータか何か) しますが、うまいこと加工すれば再利用できると思います。

[Back to top](/)

----

ここからは余談。

はがきデザインキットの最新バージョンでも住所録書き出しはできる (というか、住所録書き出ししかできない) のですが、うちの場合はこの機能で書き出すと日本語文字が "?" に置換されていました。

`hexdump` にファイルを食わせて確認したところ、いわゆる文字化けで日本語文字が "?" に見えているだけ、というのではなく完全に置き換わった状態で、気付かなかったら住所録データをロストするところでした。

うちのはがきデザインキットは Big Sur で一旦動かなくなったのを、年賀状作るために何かごにょごにょして無理やり動かしていたので、それがよくなかったのかも。

[Back to top](/)