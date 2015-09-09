---
title: phpのsyntax比較
author: kenev
layout: post
permalink: /2013/12/22/php%e3%81%aesyntax%e6%af%94%e8%bc%83/
categories:
  - PHP
  - wordpress
tags:
  - PHP
  - syntax
  - wordpress
---
<pre class="lang:php decode:true">&lt;?php if (false) : ?&gt;
ここは通らない &lt;br /&gt;
&lt;?php else : ?&gt;
&lt;b&gt;〜なんか適当に記述〜&lt;/b&gt;&lt;br /&gt;
もう一行書いてみて&lt;br /&gt;
さらにもう一行書いてみた&lt;br /&gt;
&lt;?php endif; ?&gt;

&lt;?php if(false) {
echo '&lt;i&gt;～なんか適当に記述～&lt;/i&gt;';
} else {
echo '&lt;i&gt;～Elseにてなんか適当に記述～&lt;/i&gt;&lt;br /&gt;';
echo '&lt;b&gt;もう一行書いてみる&lt;/b&gt;';
}
?&gt;</pre>

[先日の記事][1]の「はず」がちょっとひっかかっていたので実際に上のコードを書いてみて確認してみました。一応思っていた通りの動作をしているみたいです。↓↓↓

[<img class="alignnone size-medium wp-image-32" alt="スクリーンショット 2013-12-22 16.31.14" src="/images/2013/12/2a0e7e0ab7442be929b9ce969f060380-300x184.png" width="300" height="184" />][2]

&nbsp;

使い方を間違っているかもしれないけど、これだけ見ると前者の書き方の方が遥かに楽ですよね。あと、前者はNetbeansとかIDEを使った場合にコード補完してくれるのでやっぱり楽です。

 [1]: http://kenev.net/2013/12/18/php%e3%81%aeif%e6%96%87%e3%82%84while%e6%96%87%e3%81%a7%e4%bd%bf%e3%82%8f%e3%82%8c%e3%82%8b%e3%80%8c%ef%bc%88%e3%82%b3%e3%83%ad%e3%83%b3%ef%bc%89%e3%80%8d/ "phpのif文やwhile文で使われる「:（コロン）」"
 [2]: /images/2013/12/2a0e7e0ab7442be929b9ce969f060380.png
