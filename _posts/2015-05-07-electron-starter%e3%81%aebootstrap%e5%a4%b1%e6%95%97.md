---
title: electron-starterのbootstrap失敗
author: kenev
layout: post
permalink: /2015/05/07/electron-starter%e3%81%aebootstrap%e5%a4%b1%e6%95%97/
s4_url2s:
  - 
s4_image2s:
  - 
s4_ctitle:
  - 
s4_cdes:
  - 
categories:
  - Electron(Atom-Shell)
  - JavaScript
  - NodeJS
  - Web-dev
tags:
  - atom-shell
  - electron
  - NodeJS
  - starter
---
[electron-starter][1] を使ってelectronのアプリを作ってみようとドキュメント通りに以下を実行したのですが、うまくいかない。

  1. `script/bootstrap` &#8211; チェックアウト毎にこれを実行
  2. `script/build` &#8211; package.jsonとか変わったときに実行
  3. `script/run` &#8211; アプリを実行

以下のようなエラー文言が出てきてしまう。

<pre><code class="bash">dyld: lazy symbol binding failed: Symbol not found: _node_module_register
Referenced from: /Users/ほげ/Projects/electron/electron-starter-master/apm/node_modules/atom-package-manager/node_modules/keytar/build/Release/keytar.node
Expected in: dynamic lookup

dyld: Symbol not found: _node_module_register
Referenced from: /Users/ほげ/Projects/electron/electron-starter-master/apm/node_modules/atom-package-manager/node_modules/keytar/build/Release/keytar.node
Expected in: dynamic lookup
</code></pre>

Nodeは現時点の最新版（v0.12.x）を使っていたのですが、どうもこれがよろしくなさそう。  
[このIssue][2]に現象がちょっぴり似ていたのでとりあえず`nvm`を使ってNodeを複数バージョン管理できるようにし、`v0.10.x`で試しました。

## nvmのインストール

[こちら][3]で説明されていますが、とりあえず`curl`があれば

<pre><code class="bash">curl https://raw.githubusercontent.com/creationix/nvm/v0.25.1/install.sh | bash
</code></pre>

でインストール完了します。

`v0.10.x`をインストールしたかったので最新版を`nvm ls-remote`でチェックして`v0.10.38`であることがわかる。

<pre><code class="bash"># v0.10.38のNodeをインストール
nvm install v0.10.38
</code></pre>

## `script/bootstrap`再び

`script/bootstrap` する前に`node_modules`と`apm/node_modules`と`build/node_modules`の削除しておいてから実行。

<pre><code class="bash"># ./script/bootstrap
Node: v0.10.38
npm: v1.4.28
Installing build modules...
Installing apm...
Installing modules ✓
Deduping modules ✓
</code></pre>

なんかいい感じにbootstrapできたっぽいです。

## `script/build`実行

bootstrapできたのでさっそくbuild！

<pre class="lang:default decode:true"># ./script/build
Node: v0.10.38
npm: v1.4.28
Installing build modules...
Installing apm...
Installing modules ✓
Deduping modules ✓
Running "build-atom-shell" task

Running "bower:install" (bower) task
>&gt; Installed bower packages
>&gt; Copied packages to /Users/fukuyamaken/Projects/electron/electron-starter-master/static/components

Running "build" task

Running "coffee:glob_to_multiple" (coffee) task

Running "cson:glob_to_multiple" (cson) task
>&gt; 3 files compiled to JSON.

Running "generate-license:save" (generate-license) task

Running "copy-info-plist" task

Running "set-version" task

Running "generate-asar" task
Done, without errors.</pre>

かなり時間がかかりましたが`Done, without errors`って出てるしうまくいってそうです。

## `script/run`

いよいよ実行です。

<pre><code class="bash"># ./script/run
Running: /var/folders/v_/m86vd1ts65b03qdd_1984t340000gn/T/example_app-build/example_app.app/Contents/MacOS/example_app -r .
{"_":[],"d":false,"dev":false,"h":false,"help":false,"t":false,"test":false,"v":false,"version":false,"r":".","resource-path":".","resourcePath":".","$0":"/private/var/folders/v_/m86vd1ts65b03qdd_1984t340000gn/T/example_app-build/example_app.app/Contents/MacOS/example_app -r"}

App load time: 941ms

[29627:0507/065643:INFO:renderer_main.cc(212)] Renderer process started
</code></pre>

[<img class="alignnone size-large wp-image-354" src="http://kenev.net/wp-content/uploads/2015/05/3d563c34f06f938a56090af61a3b5a3c-1024x799.png" alt="スクリーンショット 2015-05-07 20.54.59" width="660" height="515" />][4]

今回はうまくいきましたね。原因究明まではあまりしたくないので、とにかく今は`node v0.10.x`でやるのが吉のようですね！

 [1]: https://github.com/atom/electron-starter
 [2]: https://github.com/atom/electron-starter/issues/51
 [3]: https://github.com/creationix/nvm
 [4]: http://kenev.net/wp-content/uploads/2015/05/3d563c34f06f938a56090af61a3b5a3c.png