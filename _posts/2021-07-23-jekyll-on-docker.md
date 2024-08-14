---
layout: post
title: GitHub Pages をローカルで表示確認
date: 2021-07-23
excerpt: Docker で jekyll 使って、GitHub Pages をローカルでお試ししてみるためのあれこれ。
author: やすお
---

## やりたいこと

[GitHub Pages](https://pages.github.com) を使うと、

* レポジトリに置いてある Markdown 記法で書いたファイルが html に自動変換される
* http://y4su0.github.io みたいな URL で公開される
* 当たり前のことながら、変更の履歴が残る

と、お手軽にWebサイトを構築できて便利なんですが、細かい設定をやろうとすると、`_config.yml` をいじったり、テンプレートをいじったりするので、可能であればコミットする前にローカル環境でお試ししておきたい。

ということで、Docker を使ってローカルで GiuHub Pages の環境を作ってみましょう、という話。

## 準備

1. [Docker Desktop](https://www.docker.com/products/docker-desktop) をインストール
2. Docker image を取得
```bash
docker pull jekyll/jekyll:pages
```
3. jekyll テーマの関連ファイルを [Supported themes / GitHub Pages](https://pages.github.com/themes/) あたりからリンクを辿っていってダウンロード

以下、GitHub Pages の関連ファイルは `~/githubpages/` 以下に置く or 置かれているという想定です。

## 環境を立ち上げる

```bash
docker run --rm --volume="~/githubpages/:/srv/jekyll" --it -p 4000:4000 jekyll serve --watch
```

これで `~/githubpages/` 以下のファイルを元にページが生成されて、https://localhost:4000 にアクセスすれば表示を確認できます。

また、`--watch` オプションを付けておけば、ファイルに変更があるたびにページがリアルタイムで再生成されるので、ブラウザをリロードすれば変更後の表示確認が即できるようになります。

あとは、`.zshrc` あたりで

```sh
alias githubpages='docker run --rm --volume="~/githubpages/:/srv/jekyll" --it -p 4000:4000 jekyll serve --watch'
``` 

とかやっておくと便利。

表示を確認できたら、そのまま `git commit` →　`git push` で公開完了。

## その他

GitHub Pages のサーバーの方では、各ページのYAML Front Matter での `layout` あたりの項目とか、`_config.yml` の　`plugins` あたりをちゃんと書かなくても良きに計らってくれますが、ローカルで生成する際はちゃんとしておかないとダメみたいです。

あと、余計なお世話ですが、テーマ関係をいじらないのであれば `~/githubpages` 以下のテーマ関係のディレクトリを `.gitignore` に書き込んでおいた方がいいかもしれません。

## 参考

* [JekyllをDokcer上で動かしてGitHub Pagesのローカル環境での確認を楽する - Qiita](https://qiita.com/shifumin/items/8d5d26dfa18d4b62d873)