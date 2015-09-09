---
title: Sequence.async
author: kenev
layout: post
permalink: /2015/05/30/sequence-async/
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
  - Lazy.js
tags:
  - functional-programming
  - javascript
  - lazy.js
---
Lazy.jsでシーケンスを生成する関数の一つ。元となったシーケンスと同一要素を含んだAsyncSequenceが生成される。このシーケンスに対して`each`を実行した場合に、各要素に関連づいた処理が非同期に実行されます。

<pre><code class="js">Sequence.async(fn)
// fn: (interval: number) =&gt; void 非同期で実行する関数
//      interval: 各非同期処理間で待機する間隔（ミリ秒）
</code></pre>

実際に使ってみます。  
本家のサンプルを元に、AsyncSequenceを二つ生成し、各々の処理で`setTimeout`による非同期処理を擬似的に行います。

<pre><code class="js">// 各非同期処理の実行間隔を100msに設定し、
// '要素の値 * 1000ms'のタイムアウトでconsole.logを実行
Lazy([1, 2, 3]).async(100).each(function(e){
    console.log('1st async processing: ', e);
    setTimeout(function(){console.log('1st async timeout: ', e); }, e * 1000);
    console.log('1st async complete: ', e);
});

// 各非同期処理の実行間隔は一定とせず、
// 完了次第次の非同期処理を実行。
// そして非同期処理内で'要素の値 * 100ms'のタイムアウトでconsole.logを実行。
Lazy([1, 2, 3]).async().each(function(e){
    console.log('2nd async processing: ', e);
    setTimeout(function(){console.log('2nd async timeout: ', e); }, e * 100);
    console.log('2nd async complete: ', e);
}); 

console.log('This is the last line!');
</code></pre>

これを実行した場合、環境によってはもしかしたら結果が変わるかもしれませんが、概ね以下のようになるかと思います。

<pre><code class="bash">This is the last line!
2nd async processing:  1 // A
2nd async   complete:  1
2nd async processing:  2
2nd async   complete:  2
1st async processing:  1 // B
1st async   complete:  1
2nd async processing:  3
2nd async   complete:  3
2nd async    timeout:  1
1st async processing:  2
1st async   complete:  2
2nd async    timeout:  2
1st async processing:  3
1st async   complete:  3
2nd async    timeout:  3
1st async    timeout:  1
1st async    timeout:  2
1st async    timeout:  3
</code></pre>

&#8216;This is the last line!&#8217;が一番に出力されるのは、それぞれのAsyncSequenceが非同期処理を行うからだということは非同期処理に慣れている人ならわかるかと思います。（ただし確実に&#8217;This is the last line!&#8217;が一番最初に出てくる保証は多分ありません）

注意が必要なのは2個目のAsyncSequenceの最初の非同期処理が一番先に実行されていることです(A)。確かに最初のAsyncSequenceに`interval`は設定していますが、一発目は即時実行かと思っていました。実際は2個目のAsyncSequenceの2番目の非同期処理が終わってから開始しています(B)。気になったのでソースを覗いてみます。

<pre><code class="js">// AsyncSequenceのコンストラクタ
function AsyncSequence(parent, interval) {
  if (parent instanceof AsyncSequence) {
    throw new Error("Sequence is already asynchronous!");
  }

  this.parent         = parent;
  this.interval       = interval;
  this.onNextCallback = getOnNextCallback(interval);
  this.cancelCallback = getCancelCallback(interval);
}

// 非同期処理の実体。結局はsetTimeoutを呼んでいる。
function getOnNextCallback(interval) {
  if (typeof interval === "undefined") {
    if (typeof setImmediate === "function") {
      return setImmediate;
    }
  }
  interval = interval || 0;
  return function(fn) {
    return setTimeout(fn, interval);
  };
}
</code></pre>

結局は`setTimeout`を呼んで非同期処理を行っているのですが、最初から`setTimeout`を`interval`で呼んでいるので、初回実行時から`interval`分のタイマーが実行されているんですね。なので`AsyncSequence`で`interval`を設定した場合に、「初回は即時実行」なんて実装は無さそうです。  
仕様としては`interval`は

> The approximate period, in milliseconds, that should elapse between each element in the resulting sequence. 

だから、要素間（非同期処理間）の間隔のことを言っている印象を受けるんですけどね。事実は上の実行例の通りみたいです。

[目次へ][1]

 [1]: http://kenev.net/2015/05/20/lazy-js-%e5%ae%9f%e8%b7%b5%e3%80%80%e7%9b%ae%e6%ac%a1/