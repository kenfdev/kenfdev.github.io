---
title: typescriptの定義ファイル(d.ts)を実際にDefinitelyTypedにPullRequestしてみた
author: kenev
layout: post
permalink: /2014/12/19/typescript%e3%81%ae%e5%ae%9a%e7%be%a9%e3%83%95%e3%82%a1%e3%82%a4%e3%83%abd-ts%e3%82%92%e5%ae%9f%e9%9a%9b%e3%81%abdefinitelytyped%e3%81%abpullrequest%e3%81%97%e3%81%a6%e3%81%bf%e3%81%9f/
categories:
  - JavaScript
  - TypeScript
tags:
  - angular-local-storage
  - definitelytyped
  - definition-file
  - github
  - javascript
  - NodeJS
  - npm
  - pull-request
  - tsd
  - typescript
  - typescript-definition-manager
  - webstorm
---
最近はもっぱらNodeJSとAngularJSに興味深々な日々です。  
Javascriptは今までただのコピペ職人だったのですが、最近は真面目に勉強しています。Javascriptの（本当に）基本的な概念を理解したところで、普段の仕事でC#とVisualStudioを使っている私としては静的型付け言語であるTypescriptに挑戦してみたくなりました。  
IDEは普段macbook proを使っているのでVMWareにWindowsを放り込んでVisualStudio Community Editionでも入れようかと思ったのですが重すぎて断念しました。ということで結局Webstormを購入してそれとなく使っています。  
Typescriptの静的型付けの性質を活かしてC#同様にIntellisense（コード補完, CodeCompletion）がとても便利。ただしサードパーティーのJavascriptライブラリを使おうと思ったとたんTypescript版を用意してくれてるライブラリなんてそうそう無いのでIntellisenseも何もあったもんじゃありません。しかもJavascript（他の言語もそうですけど）はサードパーティー製のライブラリって頻繁に使うので割と早々にこの問題に直面するかと思います。  
そこで登場するのがd.ts拡張子の「定義ファイル」。typescript側でこのファイルを参照することで、サードパーティーのライブラリでもIntellisenseがしっかりと効きます。ではこの「定義ファイル」がどこから登場するかと言うと、これがなかなか原始的で、個々人が自力で書いているだけなのです。「既存のjavascriptから定義ファイルを作ってくれるツールがあるんでしょ？」って思うかと思いますが、残念ながら現時点では無さそうです。↓参照

[Generating typescript declaration files from javascript][1]

既存のTypescriptから定義ファイルは生成できますけど、そんなものが必要になるシチュエーションが私はまだあまりわかっていません。

話がそれましたが、その「定義ファイル」をまとめられているリポジトリがあります。その名も「[DefinitelyTyped][2]」。「みんなで頑張って定義ファイル作ろう！」っていうそんな感じなプロジェクトだと思います。Githubで公開されていて「Fork→定義ファイル作成→PullRequest→本家にマージ」してもらうというそんな流れ。

非常に申し訳ないのですが、ここまでの説明はQiitaの[この記事][3]にvvakameさんがとてもわかりやすく説明しています。これを参考に実際に私の方でも定義ファイルを作ってPullRequestを発行してみてマージされるところまでを備忘録もかねて記録しておきたいと思います。（意外と技術書とかで出てくるライブラリの定義ファイルがまだDefinitelyTypedに公開されていなかったりするので、結構自分で書かないといけないことが多いです）

<!--more-->

今回Typescriptで欲しかった定義ファイルは「[angular-local-storage][4]」というlocalStorageをいい感じに使わせてくれるangularjsのモジュールです。

１．まずはGithubの[DefinitelyTyped][2]に行き、右上の「Fork」を押してForkしましょう

[<img class="alignnone size-large wp-image-164" src="/images/2014/12/1aaa99b26001207145e565a0dc597cd1-1024x651.png" alt="スクリーンショット 2014-12-18 21.24.50" width="540" height="343" />][5]

２．ForkしたリポジトリをローカルにGit Cloneしてきてまずはライブラリ名のbranchを作成しましょう（今回ならangular-local-storageブランチ）。次にライブラリ名（angular-local-storage）と一致するフォルダを作成します。

[<img class="alignnone size-large wp-image-165" src="/images/2014/12/5c929e4708c9242ab29288a28912eb0e-1024x643.png" alt="スクリーンショット 2014-12-18 21.25.45" width="540" height="339" />][6]

<span style="line-height: 1.5;">３．作成したフォルダに定義ファイルを作成します（angular-local-storage.d.ts）</span>

[<img class="alignnone size-large wp-image-167" src="/images/2014/12/8198462624c09451a98873647f89429a-1024x643.png" alt="スクリーンショット 2014-12-18 21.26.19" width="540" height="339" />][7]  
４．DefinitelyTypedに投稿する定義ファイルには以下のようにヘッダ情報を入力

<pre class="lang:js decode:true">// Type definitions for [ライブラリ名（バージョンがわかればバージョンも）]
// Project: [ライブラリのURL] 
// Definitions by: [作った人（自分）] &lt;[自分のURL]&gt; 
// Definitions: https://github.com/borisyankov/DefinitelyTyped</pre>

<img class="alignnone size-large wp-image-169" style="line-height: 1.5;" src="/images/2014/12/44886aaa4833df07d7d0bd29866a9dd5-1024x651.png" alt="スクリーンショット 2014-12-18 21.34.34" width="540" height="343" />

今回はv0.1.5が対象だったのでヘッダ情報にも書いておきました。[  
<img class="alignnone size-large wp-image-168" src="/images/2014/12/5a84d6d74c030edae3d4bd41a4a23980-1024x651.png" alt="スクリーンショット 2014-12-18 21.26.55" width="540" height="343" />][8]

５．さて、次のキャプチャではもう定義ファイルは概ね完成しています。作り方とかはTypeScriptの仕様とかが絡んでくる話なので割愛。これはとにかく対象となるライブラリのAPIを読みながら作っていくしかないと思います。コメントとかも概ね流用したものをそのまま定義ファイルに使っています。[  
<img class="alignnone size-large wp-image-170" src="/images/2014/12/495cc3ad9b0e0e3201650fc355d55e1c-1024x651.png" alt="スクリーンショット 2014-12-18 22.53.45" width="540" height="343" />][9]

６．定義ファイルが完成したところで次に必ず作らないといけないのが、その定義ファイルを使ったテストコードです。「ライブラリ名-tests.ts」を作りましょう（今回ならangular-local-storage-tests.ts）

[<img class="alignnone size-large wp-image-171" src="/images/2014/12/d35156cd09083be75a484d1a329657be-1024x643.png" alt="スクリーンショット 2014-12-18 22.54.48" width="540" height="339" />][10]

&nbsp;

７．本家の「Contribution guide」にもテストコードは対象のライブラリのサンプルコードとかを書いとけばいいよみたいなことが書いてあるので、とりあえずGithubのReadmeに載っているサンプルコードをTypescriptで書きました。以下でコンパイルが通れば多分OK。

<pre class="lang:default decode:true">tsc --noImplicitAny angular-local-storage/angular-local-storage-tests.ts</pre>

サンプルコードはこんな感じ↓  
<a style="line-height: 1.5;" href="/images/2014/12/e790a363f1564254de231c8f8505e791.png"><img class="alignnone size-large wp-image-172" src="http://kenev.net/wp-content/uploads/2014/12/e790a363f1564254de231c8f8505e791-1024x651.png" alt="スクリーンショット 2014-12-18 23.35.32" width="540" height="343" /></a>

８．ひと通り完成したところで「npm test」を走らせてテストがちゃんと通ることを確認します。

以下、確認結果。

<pre class="lang:sh decode:true">mbp:DefinitelyTyped hoge$ npm test

&gt; DefinitelyTyped@0.0.1 test /Users/hoge/Projects/Fork/DefinitelyTyped
&gt; node ./_infrastructure/runner.js --changes

=============================================================================
DefinitelyTyped Diff Detector 0.1.0
=============================================================================
All changes
-----------------------------------------------------------------------------
angular-local-storage/angular-local-storage-tests.ts
angular-local-storage/angular-local-storage.d.ts
rest/rest-tests.ts
rest/rest.d.ts
-----------------------------------------------------------------------------
Removed files
-----------------------------------------------------------------------------
no files listed here
-----------------------------------------------------------------------------
Interesting files
-----------------------------------------------------------------------------
angular-local-storage/angular-local-storage-tests.ts
angular-local-storage/angular-local-storage.d.ts
rest/rest-tests.ts
rest/rest.d.ts
not mapped? -&gt; /Users/hoge/Projects/Fork/DefinitelyTyped/when/when
-----------------------------------------------------------------------------
Queued for testing
-----------------------------------------------------------------------------
angular-local-storage/angular-local-storage-tests.ts
angular-local-storage/angular-local-storage.d.ts
rest/rest-tests.ts
rest/rest.d.ts
=============================================================================
DefinitelyTyped Test Runner 0.5.0
=============================================================================
Typescript version: 1.3.0
Typings : 2
Tests : 2
TypeScript files : 4
Total Memory : 16384 mb
Free Memory : 3650 mb
Cores : 4
Concurrent : 3
============================== Syntax checking ==============================
..
-----------------------------------------------------------------------------
Elapsed time : ~0.827 seconds (0.827s)
Successful : 100.00% (2/2)
Failure : 0.00% (0/2)
=============================== Typing tests ================================
..
-----------------------------------------------------------------------------
Elapsed time : ~0.862 seconds (0.862s)
Successful : 100.00% (2/2)
Failure : 0.00% (0/2)
=============================== Header format ===============================
..
-----------------------------------------------------------------------------
Elapsed time : ~0.002 seconds (0.002s)
Successful : 100.00% (2/2)
Failure : 0.00% (0/2)
-----------------------------------------------------------------------------
-----------------------------------------------------------------------------
Total
-----------------------------------------------------------------------------
Elapsed time : ~2.452 seconds (2.452s)
Syntax error : 0.00% (0/2)
Failed tests : 0.00% (0/2)
Invalid header : 0.00% (0/2)
Without tests : 0.00% (0/2)
-----------------------------------------------------------------------------</pre>

９．とりあえずなんとなくテストが通ってそうな予感がしたところで、Forkしたリポジトリにコミット＆プッシュしちゃいます。

<a style="line-height: 1.5;" href="/images/2014/12/bf902b86e404a39f7e074ccb734240b9.png"><img class="alignnone size-large wp-image-173" src="http://kenev.net/wp-content/uploads/2014/12/bf902b86e404a39f7e074ccb734240b9-1024x643.png" alt="スクリーンショット 2014-12-18 23.41.00" width="540" height="339" /></a>

１０．Pushが完了したところでGithubの自分のForkしたリポジトリを見ると以下のように「Compare & pull request」という項目が増えているのがわかります。

<span style="line-height: 1.5;"> </span><a style="line-height: 1.5;" href="/images/2014/12/a8acc2e917a38e63fcd7dbd6d0dfdb43.png"><img class="alignnone size-large wp-image-175" src="http://kenev.net/wp-content/uploads/2014/12/a8acc2e917a38e63fcd7dbd6d0dfdb43-1024x651.png" alt="スクリーンショット 2014-12-18 23.42.17" width="540" height="343" /></a>

&nbsp;

１１．迷わす「Compare & pull request」をクリックしてタイトルを「add ライブラリ名 definition file」みたいな感じでpull requestを作成しましょう。<a style="line-height: 1.5;" href="/images/2014/12/7dd54142413ddbdadc102da12392ab5f.png"><br /> <img class="alignnone size-large wp-image-176" src="http://kenev.net/wp-content/uploads/2014/12/7dd54142413ddbdadc102da12392ab5f-1024x651.png" alt="スクリーンショット 2014-12-18 23.42.31" width="540" height="343" /></a>

１２．pull requestが完了したところで本家の「[pull request][11]」一覧を観に行くと、ちゃんとpull requestが作成されていることがわかります。右側の印はTravis CIにてビルドがちゃんと通ったかどうかを教えてくれます。アクセスするのが早過ぎると下の画面みたいに「ビルド中&#8230;」のマークがつきます。

<a style="line-height: 1.5;" href="/images/2014/12/a3b647da8532dba7649c864bf1fb2a07.png"><img class="alignnone size-large wp-image-178" src="http://kenev.net/wp-content/uploads/2014/12/a3b647da8532dba7649c864bf1fb2a07-1024x651.png" alt="スクリーンショット 2014-12-18 23.43.19" width="540" height="343" /></a>

１３．ちょっとしてからリロードするとちゃんとビルドが通っていることが確認できて一安心。後は本家にマージするのを待つだけです。<a style="line-height: 1.5;" href="/images/2014/12/76be7256e1e3771417a84bff0b3d7659.png"><br /> <img class="alignnone size-large wp-image-179" src="http://kenev.net/wp-content/uploads/2014/12/76be7256e1e3771417a84bff0b3d7659-1024x651.png" alt="スクリーンショット 2014-12-18 23.43.26" width="540" height="343" /></a>

&nbsp;

１４．何日かしたらvvakameさんからマージ完了のメールと共に「@kenfdev（私のユーザ名） thanks mate!」 みたいなメールを受け取ることができます。

１５．上記全て完了すると[TSD][12]というTypescript Definition Managerで自分の作ったライブラリがインストールできるようになります。例えば以下のコマンドで今回の定義ファイルはインストールできます。

<pre class="lang:sh decode:true">tsd query angular-local-storage --action install</pre>

Typescriptがこの先どんどん盛り上がっていくといいですね！  
（もう盛り上がっているはず！）

 [1]: http://stackoverflow.com/questions/18301898/generating-typescript-declaration-files-from-javascript
 [2]: https://github.com/borisyankov/DefinitelyTyped
 [3]: http://qiita.com/vvakame/items/1980d4b6cc222e03fdcb
 [4]: https://github.com/grevory/angular-local-storage
 [5]: /images/2014/12/1aaa99b26001207145e565a0dc597cd1.png
 [6]: /images/2014/12/5c929e4708c9242ab29288a28912eb0e.png
 [7]: /images/2014/12/8198462624c09451a98873647f89429a.png
 [8]: /images/2014/12/5a84d6d74c030edae3d4bd41a4a23980.png
 [9]: /images/2014/12/495cc3ad9b0e0e3201650fc355d55e1c.png
 [10]: /images/2014/12/d35156cd09083be75a484d1a329657be.png
 [11]: https://github.com/borisyankov/DefinitelyTyped/pulls
 [12]: http://definitelytyped.org/tsd/
