---
title: Ix.Enumerable.range
author: kenev
layout: post
permalink: /2015/05/21/ix-enumerable-range/
s4_url2s:
  - 
s4_image2s:
  - 
s4_ctitle:
  - 
s4_cdes:
  - 
categories:
  - Functional Programming
  - IxJS
tags:
  - Ix.Enumerable.range
  - ix.js
  - lazy.js
---
IxJSでシーケンスを生成する関数の一つ。指定された初期値から指定された個数分の値を生成する。ステップは+1固定。

<pre><code class="js">Ix.Enumerable.range(start, count);
// start: 初期値
// count: 生成する個数
</code></pre>

JSFiddle  


Lazy.jsのLazy.rangeと比較すると汎用性はあまりない。ただし、Lazy.js同様にIx.Enumerable.generateを使うことで比較的容易に同じものを実装することができる。(じゃあIx.Enumerable.generate使えばいいのでは？というのは無しで）

JSFiddle(Lazy.jsのLazy.rangeをIx.jsで実現してみた)  


Lazy.js関連関数  
[Lazy.range][1]

[目次へ][2]

 [1]: http://kenev.net/2015/05/21/lazy-range/
 [2]: http://kenev.net/2015/05/20/ix-js-%e5%ae%9f%e8%b7%b5%e3%80%80%e7%9b%ae%e6%ac%a1/