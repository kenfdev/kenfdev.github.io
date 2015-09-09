---
title: Ix.Enumerable.generate
author: kenev
layout: post
permalink: /2015/05/20/ix-enumerable-generate/
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
tags:
  - generate
  - ix.js
  - javascript
---
IxJSでシーケンスを生成する関数。

<pre><code class="js">Ix.Enumerable.generate(initialState, condition, iterate, resultSelector)
// initialState: 初期値
// condition: この条件を満たす間シーケンスを生成する
// iterate: この関数を使ってイテレートする
// resultSelector: この結果がyieldされる
</code></pre>

「0から始まって、5より小さい間1ずつ増えていく値を生成するシーケンス」は以下のjsfiddleで確認できます。

Lazy.js関連関数  
[Lazy.generate][1]

[目次へ][2]

 [1]: http://kenev.net/2015/05/20/lazy-generate/
 [2]: http://kenev.net/2015/05/20/ix-js-%E5%AE%9F%E8%B7%B5%E3%80%80%E7%9B%AE%E6%AC%A1/