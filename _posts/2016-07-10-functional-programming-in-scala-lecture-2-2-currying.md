---
title: 'Functional Programming in Scala - Currying'
author: kenev
layout: post
permalink: /2016/07/10/functional-programming-in-scala-lecture-2-2-currying/
s4_url2s:
  -
s4_image2s:
  -
s4_ctitle:
  -
s4_cdes:
  -
categories:
  - scala
tags:
  - coursera
  - scala
---

CourseraでScalaの勉強をしている今日このごろ。関数型なマインドが備わっていないので苦戦していますが、些細なことでもメモを残しながら考えを整理しておきたいです。

# 指定した範囲の数値に式を与え、その結果の積や和を求める

例えば `product(x => x * x)(3, 4)` と渡した場合に `(3 * 3) * (4 * 4)` となって答えが `144` となる実装を考える。そしたら、下のように書くことができる。

```scala
def product(f: Int => Int)(a: Int, b: Int): Int =
    if (a > b) 1
    else f(a) * product(f)(a + 1, b)
```

例えば、和を求めたかったら下のように書くこともできる。

```scala
def sum(f: Int => Int)(a: Int, b: Int): Int =
    if (a > b) 0
    else f(a) + sum(f)(a + 1, b)
```

# mapReduceを作ってみる

上の例で `product` と `sum` が似ているのがわかります。さらに汎用的な関数となる `mapReduce` なるものを作るとなったら、下のように書くことができます。

```scala
def mapReduce(f: Int => Int, combine: (Int, Int) => Int, zero: Int)(a: Int, b: Int): Int =
    if (a > b) zero
    else combine(f(a), mapReduce(f, combine, zero)(a + 1, b))
```

これでさきほどの `product` や `sum` を書き直した場合、下のようにリファクタできます。

```scala
def product(f: Int => Int)(a: Int, b: Int): Int = mapReduce(f, (x, y) => x * y, 1)(a, b)

def sum(f: Int => Int)(a: Int, b: Int): Int = mapReduce(f, (x, y) => x + y, 0)(a, b)
```

