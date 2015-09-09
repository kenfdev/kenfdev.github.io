---
title: Lazy.range
author: kenev
layout: post
permalink: /2015/05/21/lazy-range/
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
  - Lazy.js
tags:
  - lazy.js
  - lazy.range
---
Lazy.jsでシーケンスを生成する関数の一つ。返されるシーケンスはGeneratedSequence。初期値から終了値 **手前** まで、指定されれば指定されたステップで加算された値が生成される。 **異常な引数が渡された場合は空のシーケンスが返される。**

<pre class="lang:js decode:true ">Lazy.range(stop);
// stop: 終了値
// 0からstopまでの値が+1されながら生成される

Lazy.range(start, stop);
// start: 初期値
//  stop: 終了値
// startからstopまでが生成される。
// start &lt; stopならステップは+1
// start &gt; stopならステップは-1

Lazy.range(start, stop, step);
// start: 初期値
//  stop: 終了値
//  step: ステップ
// startからstopまで、stepだけ加算されながら生成される</pre>

実際の実装をソースから引っ張ってきました。内部的には[Lazy.generate][1]を使っているのがわかりますね。

<pre class="lang:js decode:true ">Lazy.range = function range() {
  var start = arguments.length &gt; 1 ? arguments[0] : 0,
  stop = arguments.length &gt; 1 ? arguments[1] : arguments[0],
  step = arguments.length &gt; 2 && arguments[2];

  if (step === false) {
    step = stop &gt; start ? 1 : -1;
  }

  if (step === 0) {
    return Lazy([]);
  }

  return Lazy.generate(function(i) { return start + (step * i); })
    .take(Math.ceil((stop - start) / step));
};
</pre>

JSFiddleで結果を確認してみましょう  


IxJS関連関数  
[Ix.Enumerable.range][2]

[目次へ][3]

 [1]: http://kenev.net/2015/05/20/lazy-generate/
 [2]: http://kenev.net/2015/05/21/ix-enumerable-range/
 [3]: http://kenev.net/2015/05/20/lazy-js-%e5%ae%9f%e8%b7%b5%e3%80%80%e7%9b%ae%e6%ac%a1/