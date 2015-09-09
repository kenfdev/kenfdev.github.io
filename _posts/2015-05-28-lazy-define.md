---
title: Sequence.define
author: kenev
layout: post
permalink: /2015/05/28/lazy-define/
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
  - ix.js
  - javascript
  - lazy.js
---
Sequenceから派生したカスタムシーケンスが作成可能になる。現段階で筆者がLazy.jsを実運用で使ったことがないので、このメソッドの使い道がいまいちわからないです。が、本家のサンプルにもあるように、各シーケンス要素においてログをとるなどの副作用をシーケンス内に発生させることができそうです。

<pre><code class="js">Sequence.define(methodName, overrides);
// methodName: シーケンスを生成するときの名前。配列指定で複数設定することも可能。
// overrides: カスタムシーケンスの定義。getIterator, eachの何れかは必ず定義する必要があります。initを定義することでシーケンス作成時に実行する関数も指定可能。
</code></pre>

JSFiddleで結果を確認してみましょう（JavaScriptのみ）  


実行時のconsole↓↓↓

<pre><code class="bash"># 要素が表示される順番も重要。1, 2, 3のあと2が表示されるわけではない
 first log:  1
 first log:  2
second log:  2
 final log:  2
 first log:  3
</code></pre>

[目次へ][1]

 [1]: http://kenev.net/2015/05/20/lazy-js-%e5%ae%9f%e8%b7%b5%e3%80%80%e7%9b%ae%e6%ac%a1/