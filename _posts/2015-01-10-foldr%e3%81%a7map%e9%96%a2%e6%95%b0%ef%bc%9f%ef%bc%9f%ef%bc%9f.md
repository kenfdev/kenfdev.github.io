---
title: foldrでmap関数？？？
author: kenev
layout: post
permalink: /2015/01/10/foldr%e3%81%a7map%e9%96%a2%e6%95%b0%ef%bc%9f%ef%bc%9f%ef%bc%9f/
categories:
  - Haskell
tags:
  - foldr
  - functional-programming
  - ghci
  - haskell
  - map
---
最近大川徳之さんの「関数プログラミング実践入門」を購入して再びHaskellをちらほら触っています。  
foldr関数に差し掛かったところなのですが、割と混乱してしまったので掘り下げてみました。  
最初に引っかかったのがこの文章です。

「foldrは実は、リストの(:)と[]をそれぞれ関数と初期値で置換する関数です。」

なんとなくわかるような、でもわかってないような、そしてやっぱりわかってなかった自分がそこにはいました。私は凡人以下のプログラミング脳、数学脳なのでここでストップ。。。Google先生登場です。

[Functional Programming][1]

上のサイトで割とイメージがつかめました。  
まず、n個の要素から構成されるリストxs（[x1,x2, &#8230; ,xn]）は木構造で見ると以下のように描けます。

[<img class="alignnone wp-image-200 size-medium" src="http://kenev.net/wp-content/uploads/2015/01/aa69538e27c74737e9247e442af9d657-300x285.png" alt="スクリーンショット 2015-01-10 22.23.17" width="300" height="285" />][2]

&nbsp;

つまり、大川さんが仰っていた「foldrはリストの(:)と[]をそれぞれ関数と初期値で置換する関数」というのは木構造で表現すると以下のことです。  
[<img class="alignnone size-medium wp-image-201" src="http://kenev.net/wp-content/uploads/2015/01/f0a8e60e85be2377fd5d36036c3cca41-300x286.png" alt="スクリーンショット 2015-01-10 22.46.47" width="300" height="286" />][3]

リストの(:)と[]が関数fと初期値zに置き換わっていることがわかります。これをさらに具体例を挙げてみましょう。例えば[1,2,3]というリストがあったとします。このリストに対して初期値を4として右側から乗算(*)で畳み込む（foldr）を適用した場合以下のようになります。

1 \* (2 \* (3 * 4)) = 24

さらにリストと木構造で描くと以下の通り。

[<img class="alignnone wp-image-202 size-large" src="http://kenev.net/wp-content/uploads/2015/01/d251fdbbb45c754e7cf5d6c9d46a5a94-1024x307.png" alt="スクリーンショット 2015-01-10 22.53.51" width="540" height="162" />][4]

&nbsp;

なるほど！確かに(:)と[]が関数と初期値に変わっている！結構スッキリです。

# しかし

一難去ってまた一難。  
その直後くらいにしれっと書いてある文章でまたつまづきます。

「foldrからはmapが作れるなど、汎用的で強力な関数になっています」

<pre class="lang:haskell decode:true">map' :: (a -&gt; b) -&gt; [a] -&gt; [b]
map' f = foldr ((:).f) []</pre>

<span style="line-height: 1.5;">わからん。いや、作れそうな雰囲気はなんとなくわかるんですけど、これを自分でちゃんと理解できていないこともよくわかる。まずmap関数はと言いますと、指定された関数をリストのそれぞれの要素に適用してリストを生成する関数です。</span>

<pre class="lang:haskell decode:true">*Main&gt; map (*2) [1,2,3]
[2,4,6]</pre>

<span style="line-height: 1.5;">となります。イメージ的には多分↓</span>

[<img class="alignnone size-large wp-image-203" src="http://kenev.net/wp-content/uploads/2015/01/565dcfb4ac4971dc1ac20313fbe182ae-1024x412.png" alt="スクリーンショット 2015-01-10 23.16.48" width="540" height="217" />][5]

&nbsp;

お？ということは左から右の結果を出す為に(:)と[]を置換すればfoldr関数で同様の処理ができるわけだ！ということで以下のように描けることがわかります。（ラムダ式＝図の中のfと思ってください）

[<img class="alignnone size-large wp-image-204" src="http://kenev.net/wp-content/uploads/2015/01/e4774a10e31ae5a7b2c01ee2019d0e15-1024x360.png" alt="スクリーンショット 2015-01-10 23.18.06" width="540" height="190" />][6]

&nbsp;

ラムダ式の内容は「関数(*2)を適用してリストysの先頭に要素を挿入する」ということなので上の例での((:).f)と同等の意味となります。ということでmap関数をfoldr関数で表すと以下のようになるのです。

<pre class="lang:haskell decode:true">map' :: (a -&gt; b) -&gt; [a] -&gt; [b]
map' f = foldr ((:).f) []
−− map' f as = foldr ((:).f) [] asと一緒だし
-- map' f x:xs = foldr (\y ys -&gt; f y:ys) [] xsとも一緒
</pre>

いやー、脳みその体操が足りてない足りてない！  


<noscript>
</noscript>

 [1]: http://www.pling.org.uk/cs/fun.html
 [2]: http://kenev.net/wp-content/uploads/2015/01/aa69538e27c74737e9247e442af9d657.png
 [3]: http://kenev.net/wp-content/uploads/2015/01/f0a8e60e85be2377fd5d36036c3cca41.png
 [4]: http://kenev.net/wp-content/uploads/2015/01/d251fdbbb45c754e7cf5d6c9d46a5a94.png
 [5]: http://kenev.net/wp-content/uploads/2015/01/565dcfb4ac4971dc1ac20313fbe182ae.png
 [6]: http://kenev.net/wp-content/uploads/2015/01/e4774a10e31ae5a7b2c01ee2019d0e15.png