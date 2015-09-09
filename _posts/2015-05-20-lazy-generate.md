---
title: Lazy.generate
author: kenev
layout: post
permalink: /2015/05/20/lazy-generate/
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
  - JavaScript
tags:
  - javascript
  - lazy.js
---
Lazy.jsでシーケンスを生成する関数の一つ。返されるシーケンスは`GeneratedSequence`となります。

<pre><code class="js">Lazy.generate(generatorFn, length);
// generatorFn: function(number) この関数に順次数値が渡される(0,1,..)
// length: シーケンスの長さを指定する場合に設定
</code></pre>

`[0,1,2,3,4]`のシーケンスを作成したjsfiddleが以下の通りです。

Ix.js関連関数  
[Ix.Enumerable.generate][1]

[目次へ][2]

 [1]: http://kenev.net/2015/05/20/ix-enumerable-generate/
 [2]: http://kenev.net/2015/05/20/lazy-js-%E5%AE%9F%E8%B7%B5%E3%80%80%E7%9B%AE%E6%AC%A1/