---
title: Lazy.repeat
author: kenev
layout: post
permalink: /2015/05/26/lazy-repeat/
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
  - Lazy.js
tags:
  - ix.js
  - javascript
  - lazy.js
---
Lazy.jsでシーケンスを生成する関数の一つ。返されるシーケンスはGeneratedSequence。繰り返しの回数を省略した場合は無限シーケンスになる。

<pre><code class="js">Lazy.repeat(value, count);
// value: 繰り返す対象となる値
// count?: valueを繰り返す回数。省略した場合は無限シーケンス。
</code></pre>

実際のソースをみてもわかるように[Lazy.generate][1]のエイリアスになっているだけです。`Lazy.generate(generatorFn, length)`における`generatorFn`で引数を無視して`return value`だけしているところが味噌ですね。

<pre><code class="js">Lazy.repeat = function repeat(value, count) {
  return Lazy.generate(function() { return value; }, count);
};
</code></pre>

JSFiddleで結果を確認してみましょう  


IxJS関連関数  
[Ix.Enumerable.repeat][2]

[目次へ][3]

 [1]: http://kenev.net/2015/05/20/lazy-generate/
 [2]: http://kenev.net/2015/05/26/ix-enumerable-repeat/
 [3]: http://kenev.net/2015/05/20/lazy-js-%e5%ae%9f%e8%b7%b5%e3%80%80%e7%9b%ae%e6%ac%a1/