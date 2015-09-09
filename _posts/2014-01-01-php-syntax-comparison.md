---
title: php syntax comparison
author: kenev
layout: post
permalink: /2014/01/01/php-syntax-comparison/
categories:
  - 未分類
---
<pre class="lang:php decode:true">&lt;?php if (false) : ?&gt;
This will not get displayed &lt;br /&gt;
&lt;?php else : ?&gt;
&lt;b&gt;Some text to be displayed&lt;/b&gt;&lt;br /&gt;
And another line to be displayed&lt;br /&gt;
And yet another line to be displayed&lt;br /&gt;
&lt;?php endif; ?&gt;

&lt;?php if(false) {
echo '&lt;i&gt;This won't get displayed&lt;/i&gt;';
} else {
echo '&lt;i&gt;Some text to be displayed&lt;/i&gt;&lt;br /&gt;';
echo '&lt;b&gt;And another line to be displayed&lt;/b&gt;'; }
?&gt;</pre>

&nbsp;

I&#8217;ve been wondering about the post I made a few days ago and actually wrote some code which turned out be as expected.

[<img class="alignnone size-medium wp-image-40" alt="スクリーンショット 2013-12-30 23.40.05" src="http://kenev.net/wp-content/uploads/2013/12/dbe9ce71e0e0aedc51426001d8cde826-300x120.png" width="300" height="120" />][1]

I&#8217;m not sure if this is the right way to use php but it&#8217;s definitely easier to write html with the former syntax. And in addition, in Netbeans you can take advantage of the code completion feature if you use the former syntax which is quite useful to code efficiently.

 [1]: http://kenev.net/wp-content/uploads/2013/12/dbe9ce71e0e0aedc51426001d8cde826.png