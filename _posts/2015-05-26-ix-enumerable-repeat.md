---
title: Ix.Enumerable.repeat
author: kenev
layout: post
permalink: /2015/05/26/ix-enumerable-repeat/
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
IxJSでシーケンスを生成する関数の一つ。指定した値を繰り返し生成します。繰り返す回数を省略した場合は無限シーケンスを生成します。

<pre><code class="js">Ix.Enumerable.repeat(value, count);
// value: この値が繰り返されます
// count?: 繰り返す回数。省略時は無限シーケンスになる。
</code></pre>

実装方法を見ると結構おもしろい。

<pre class="lang:js decode:true ">Enumerable.repeat = function (value, repeatCount) {
    return new Enumerable(function () {
        var count = repeatCount == null ? -1 : repeatCount, hasRepeatCount = repeatCount != null;
        return enumeratorCreate( // enumeratorCreateの定義は下
             // moveNext
            function () {
                if (count !== 0) {
                    hasRepeatCount && count--; // repeatCountが指定されない場合は必ずtrueを返す。これが無限シーケンスの仕掛け。
                    return true;
                } else {
                    return false;
                }
            },
            // getCurrent
            function () { return value; } // 必ずvalueを生成。これが繰り返しの仕掛け
        );
    });
}; 

var enumeratorCreate = Enumerator.create = function (moveNext, getCurrent, dispose) {
    var done = false;
    dispose || (dispose = noop);
    return new Enumerator(function () {
        if (done) {
            return false;
        }
        var result = moveNext(); // 必ずtrue
        if (!result) {
            done = true;
            dispose();
        }
        return result;
    }, function () { return getCurrent(); }, function () {
        if (!done) {
            dispose();
            done = true;
        }
    });
};</pre>

&nbsp;

JSFiddle  


Lazy.js関連関数  
[Lazy.repeat][1]

[目次へ][2]

 [1]: http://kenev.net/2015/05/26/lazy-repeat/
 [2]: http://kenev.net/2015/05/20/ix-js-%e5%ae%9f%e8%b7%b5%e3%80%80%e7%9b%ae%e6%ac%a1/