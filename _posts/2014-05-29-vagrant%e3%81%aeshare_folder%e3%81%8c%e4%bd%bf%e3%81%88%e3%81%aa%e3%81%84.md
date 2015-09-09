---
title: vagrantのshare_folderが使えない
author: kenev
layout: post
permalink: /2014/05/29/vagrant%e3%81%aeshare_folder%e3%81%8c%e4%bd%bf%e3%81%88%e3%81%aa%e3%81%84/
categories:
  - Vagrant
tags:
  - deprecated
  - share_folder
  - synced_folder
  - vagrant
---
vagrant Up and Running

を読んでると、初めてのconfigにこんな設定をしてくださいって書いてあります。

<pre class="lang:ruby decode:true">Vagrant::Config.run do |config| 
  config.vm.box = "precise64"
  config.vm.share_folder "v-root", "/vagrant", "."
  config.vm.provision "shell" do |s|
    s.path = "script.sh"
  end 
end</pre>

これをそのまま設定してvagrant upすると下のように怒られます。

<pre class="lang:default decode:true">There are errors in the configuration of this machine. Please fix
the following errors and try again:

vm:
* The following settings shouldn't exist: share_folder</pre>

誤入力したのかと思いましたがダブルチェックしても確かに合っています。「vagrant share\_folder」で調べるといくつかそれっぽいエントリを発見。まず、本家のドキュメントがトップにヒットしますがとりあえずタイトルが「synced folders」でおしいんですよね。私が知りたいのは「share\_folder」であって「sync folder」ではないのです。続いて下記stackoverflowにてビンゴな質問発見。

[config.vm.share_folder setting not found][1]

まさしく同じ症状。fgrehmさんの答えによるとvagrant 1.1以降は「share\_folder」じゃなくて「synced\_folder」ですよ、と。なるほど、それで本家の「Synced Folders」のページがトップにヒットするんですね。どこかにshare_folderはdeprecatedですよ、みたいなこと書いてないのでしょうか。。。ついでに下の質問もチェック。

[Vagrant shared and synced folders][2]

要約すると。下の設定にしたのになんで/vagrantがshare folderになってるの？

<pre class="lang:ruby decode:true">Vagrant::Config.run do |config|

  config.vm.define :foo do |cfg|
    cfg.vm.box     = 'foo'
    cfg.vm.host_name = "foo.localdomain.local"
    cfg.vm.network :hostonly, "192.168.123.10"
  end

  Vagrant.configure("2") do |cfg|
    cfg.vm.customize [ "modifyvm", :id , "--name", "foo" , "--memory", "2048", "--cpus", "1"]
    cfg.vm.synced_folder "/tmp/", "/tmp/src/"
  end

end</pre>

というもの。回答を要約すると、デフォルトでプロジェクトディレクトリ（Vagrantfileがあるディレクトリ）は共有ディレクトリとして組み込まれるようです。[ドキュメント][3]にもしっかり明記されています。ちなみにこの挙動をやめさせる為には以下をVagrantfileに書いておけばOK。

<pre class="lang:ruby decode:true">cfg.vm.synced_folder ".", "/vagrant", disabled: true</pre>

/tmp/がなんでマウントされなかったかについては特にリアクションがなかったのですが、デバッグモードでvagrant upやvagrant reloadを行えば詳細が表示されるようですね。この後に特にリプライも無いので、きっと権限の問題だったのでしょう。きっと。

<pre class="lang:default decode:true">vagrant up --debug
vagrant reload --debug</pre>

メモメモ。

ちなみに[旧ドキュメント][4]にはshare_folderの記述がもちろん残っています。このドキュメントをみると怪しげなアラートがページの上にあるのですが、何故か英語ではない。

A versão 1.1 do Vagrant foi lançada. A documentação que você está lendo é para o Vagrant 1.0.x. Leia mais sobre o Vagrant 1.1 no anúncio de lançamento. Acesse agora a documentação da versão 1.1.

とりあえずバージョンのことを何か言ってそうな雰囲気。Google翻訳さんにお願いしたらポルトガル語でそれっぽい英訳が出てきました。

Version 1.1 was released Vagrant. The documentation you are reading is for Vagrant 1.0.x. Read more about Vagrant 1.1 in the release announcement. Now access the documentation for version 1.1.

Google翻訳さまさま。「あなたが読んでいるドキュメントはVagrant 1.0.xのものだから、1.1以降のドキュメント読んでね」ってことですね。

すっきりしました。

 [1]: http://stackoverflow.com/questions/18752778/config-vm-share-folder-setting-not-found
 [2]: http://stackoverflow.com/questions/18528717/vagrant-shared-and-synced-folders
 [3]: http://docs.vagrantup.com/v2/synced-folders/index.html
 [4]: http://friendsofvagrant.github.io/v1/docs/config/vm/share_folder.html