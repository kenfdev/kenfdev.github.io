---
title: Rails 4 and Bootstrap 3
author: kenev
layout: post
permalink: /2014/02/14/rails-4-and-bootstrap-3/
categories:
  - Rails
tags:
  - bootstrap-sass
  - gemfile
  - upgrade
---
最近３度目くらいのMihael HertlのRuby on Rails Tutorialをやり終わったのですが、今度はBootstrapを2から3にバージョンアップして使いたいって気分になったのでGemfileを更新しました。

<pre class="lang:ruby decode:true ">gem 'bootstrap-sass', '~&gt; 3.1.0'</pre>

&nbsp;

簡単に更新ができたので勢いにのってそのまま「col-lg-*」でグリッドを定義してみようとHAMLに手を加えて表示してみたところ、何も変わりませんでした。glyphiconも使ってみましたが何も表示されず。。。サーバを再起動してみたりGemfileをUpgradeしてみたり、思いつきでいろいろ試すも反応なし！

そこでBootstrap2のspan3とかを使ってみたら反映されてしまったではありませんか。GemListを確かめても確かにBootstrap3がインストールされているのになぜ。。。

グーグル先生に聞いてみたところ、どうやらrakeの「tmp:clear」でキャッシュを消すと直ることあるよ、とのこと。さっそく

<pre class="lang:ruby decode:true ">rake tmp:clear</pre>

&nbsp;

を実行。サーバを再起動。見事にcol-lg-*でグリッド配置可能に！glyphiconもちゃんと表示されるようになりました。  
なにやらGemをアップグレードしただけではBootstrap2のキャッシュが残ってしまう場合があるみたいです。