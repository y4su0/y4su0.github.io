---
layout: post
title: anacondaのインストールで余計な (base)
date: 2021-07-23
author: おぎうちやすお
excerpt: anaconda をインストールした時に現れる余計な (base) への対応
---

mac に anaconda をインストールした時に、コマンドプロンプト先頭に↓こんな感じで (base) と付くのが鬱陶しいのでその対策です。


```
(base)yasuo@iMac:~ $ _
```
macOS Big Sur でしか確認できていませんが、多分、mac 以外でも同じことが起こって、同じ対策でいけるはず。

"[MacOS(OSX) - macのターミナルに(base)と表示され場合の対応方法 - Qiita](https://qiita.com/Atsushi_/items/253d3df5be8ce799dbf0)" とか "[command line - Why does "(base)" appear in front of my terminal prompt? - Ask Ubuntu](https://askubuntu.com/questions/1026383/why-does-base-appear-in-front-of-my-terminal-prompt)の内容ほぼそのままです。

```bash
conda deactivate
conda config --set auto_activate_base False
```

---

anaconda が .zshrc に余計なことを書くのが悪い。

```bash
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/usr/local/Caskroom/miniconda/base/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/usr/local/Caskroom/miniconda/base/etc/profile.d/conda.sh" ]; then
        . "/usr/local/Caskroom/miniconda/base/etc/profile.d/conda.sh"
    else
        export PATH="/usr/local/Caskroom/miniconda/base/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
```
