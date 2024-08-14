---
layout: post
date: 2023-11-25 16:02
author: やすお
title: 自然対数の底をモンテカルロ法で求める
excerpt: 自然対数の底をモンテカルロ法で求める (そのまんま)
math: true
modified_date: 2023-12-31 17:13
---

## 最初に

$[0, 1)$ の範囲の独立な乱数を足して $1$ を超えるまでの個数の期待値は $e$ (自然対数の底)、という定理があります。証明は[愛知教育大の鈴木先生の論文](https://aue.repo.nii.ac.jp/records/2871)あたりを参照。

で、この定理を使えばモンテカルロ法で $e$ を求めることができるので Python で試してみた、というお話。

## Pythonで書いてみる

1億回試してみて個数の平均を取る (ついでに処理時間を計測する) Python コードは以下の通りです。いろいろ効率が悪い書き方かもしれないけど、素直に書いたらこんなものでしょう。

```python
from numpy import random
import time

loop = 10 ** 8
num_rand = 0

start = time.process_time()
for i in range(loop):
    sum_rand = 0
    while True:
        sum_rand += random.rand()
        num_rand += 1
        if (sum_rand > 1.0):
            break

print (time.process_time() - start)
print (num_rand / loop)
```
Apple M2 搭載の MacBook Air 15inch (2023) + Python 3.11.6 という環境でこれを実際に動かしてみると出力は以下の通り。

```
2.71844723
52.353312
```
$e$ の値が $2.718281828459045...$ なので、52秒かけて有効数字4桁ぐらいまで求められることになります。

## もっと効率のよい方法

$e$の値に関しては

$$
e=\sum_{n=0}^{\infty}\frac{1}{n!}
$$

というもっと有名な定理があって、これを Python で書くと以下の通りになります。

```python
from functools import cache
import time

@cache
def factorial(n: int):
    if n == 0:
        return 1
    return n * factorial(n - 1)

e = 0

start = time.process_time()
for i in range(0, 10):
    e = e + 1.0 / factorial(i)

print(time.process_time() - start)
print(e)
```
コード通り $n=10$ で計算を打ち切ったら出力は以下の通り。

```
8.100000000021979e-05
2.7182815255731922
```
ということで、10,000分の1秒ほどで有効数字6桁の精度で $e$ の値が求められます。

(2023/12/31 追記)

mathモジュールにfactorial()という階乗を求める関数があったので、自前で実装せずにこっちを使ってみる。

```python
import math
import time

e = 0

start = time.process_time()
for i in range(0, 10):
    e = e + 1.0 / math.factorial(i)
print(time.process_time() - start)
print(e)
```

```
4.499999999996174e-05
2.7182815255731922
```

ということで、当たり前ながら結果は変わらないけど、所要時間はさらに約半分になりました。

(以上、追記)

効率のいい方法がある時はそっちを使った方がいいですよ、というお話でした。
