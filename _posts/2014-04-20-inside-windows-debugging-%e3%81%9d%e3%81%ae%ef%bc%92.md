---
title: Inside Windows Debugging その２
author: kenev
layout: post
permalink: /2014/04/20/inside-windows-debugging-%e3%81%9d%e3%81%ae%ef%bc%92/
categories:
  - Windows
tags:
  - hyper-v
  - kernel-debugging
  - vmware-fusion
  - windbg
---
Mac OSXのVMWare Fusion上にWindows 8.1を動かした上でHyper-VにWindows 7を動かしてKernel Debuggingしてみました。

無能な為トラップにはまりまくって数時間何も進まなかったのでメモ。

まず、元々はMac OSXのVMWare Fusion上にWindows 8.1とWindows 7を載せてVMware FusionのVM同士をシリアルポートで繋いでWinDbgするつもりだったのですが、どうにもVM⇔VMのシリアルポートのやりとりが上手く設定できなかったので断念。多分pipeの指定方法が間違っていたのですが、原因特定に時間がかかりそうだった＋&#8221;Inside Windows Debugging&#8221;の本の中ではHyper-Vでの構築方法が書いてあったのであきらめたしだいです。（[この記事][1]を発見したので、そのうちもう一回試してみます）

[<img class="alignnone size-medium wp-image-109" src="http://kenev.net/wp-content/uploads/2014/04/06f7f14b566d2ea544e754d9f3bdf24b-300x192.png" alt="スクリーンショット 2014-04-20 21.57.25" width="300" height="192" />][2]

Windows8.1がDebuggerでWindows7がDebuggeeになります。  
まず、Windows8.1でHyper-Vを有効にするためには少しだけイレギュラーな設定にしなければいけません。VMWare Fusionの「仮想マシン＞設定」から「一般」を選択して「OS」の項目を「Hyper-V(サポートなし)」に設定します。

[<img class="alignnone size-medium wp-image-110" src="http://kenev.net/wp-content/uploads/2014/04/7efc5fcd768886539a64544f8115b06f-300x147.png" alt="スクリーンショット 2014-04-20 22.02.24" width="300" height="147" />][3]

&nbsp;

また、「プロセッサとメモリ」の「詳細オプション」にて「この仮想マシンのハイパーバイザーアプリケーションを有効にする」にチェックが入っていることを確認してください。この設定を行うことでWindows8.1の「コントロールパネル＞プログラム＞Windowsの機能の有効化または無効化」にて「Hyper-V プラットフォーム」がインストール可能となります。

[<img class="alignnone size-medium wp-image-111" src="http://kenev.net/wp-content/uploads/2014/04/bab5c9dec7232e164c0ec6ba9edc26a6-300x245.png" alt="スクリーンショット 2014-04-20 22.08.18" width="300" height="245" />][4]

&nbsp;

後は通常通りHyper-VにてゲストOS（私の場合はWindows7なので以後はWindows7と言います）を「新規＞仮想マシン」でインストールします。

さて、Windows7のインストールが完了したらDebugできるようにするおまじないな設定をします。「Windowsキー+R」で「msconfig」と入力してmsconfigを立ち上げる。ここで「ブート」タブの詳細設定にて「デバッグ」にチェックを入れます（他はいじらない）。ここでいったんWindows7にはシャットダウンで眠ってもらいます。

[<img class="alignnone size-medium wp-image-113" src="http://kenev.net/wp-content/uploads/2014/04/29461ec9a6e25bca35135dcf1be59552-300x199.png" alt="スクリーンショット 2014-04-20 22.16.50" width="300" height="199" />][5]

Hyper-Vの設定に戻って仮想シリアルポートの設定を行います。DebuggerとDebuggeeを繋ぐ要！仮想マシンの設定にて「COM1」という設定項目があるので、そこの設定を以下の図のように「名前付きパイプ」で「Debug」（この名称は任意）と設定。ここで表示される「名前付きパイプのパス」を使うのでメモっておきます。

[<img class="alignnone size-medium wp-image-112" src="http://kenev.net/wp-content/uploads/2014/04/5c481ceef3483ab715c6a039d3fd0835-300x262.png" alt="スクリーンショット 2014-04-20 22.20.32" width="300" height="262" />][6]

&nbsp;

いよいよKernel Debugを実行することが可能になります。Windows7を立ち上げます。そしてHostであるWindows8.1側にてWinDbgを管理者権限で立ち上げます。「Ctrl + k」でKernel Debuggingのダイアログを開いて、以下のように設定。Portで先ほどの名前付きパイプのパスを使うのです。

[<img class="alignnone size-medium wp-image-114" src="http://kenev.net/wp-content/uploads/2014/04/058f868736b038228f5422a3e1549c5c-300x241.png" alt="スクリーンショット 2014-04-20 22.25.14" width="300" height="241" />][7]

&nbsp;

OKを押下するといよいよ開始！

[<img class="alignnone size-medium wp-image-115" src="http://kenev.net/wp-content/uploads/2014/04/f1dd4298f116f21d0bfbdf6539ec7c13-300x173.png" alt="スクリーンショット 2014-04-20 22.26.43" width="300" height="173" />][8]

&nbsp;

と、思ったら「Waiting to reconnect&#8230;」って表示されたままだしステータスバーには「Debuggee not connected」って書いてあるしで、何か設定を間違えたのかな？とここで無駄に（？）かなり悩んでしまいました。

が、breakで介入しないと何も始まらないんですね。本当に初心者なのでさっぱりわかっていませんでした（お恥ずかしい）。ということで「Debug > Break」を実行するとこんな感じ↓↓↓

> \***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\****  
> \* \*  
> \* You are seeing this message because you pressed either \*  
> \* CTRL+C (if you run kd.exe) or, \*  
> \* CTRL+BREAK (if you run WinDBG), \*  
> \* on your debugger machine&#8217;s keyboard. \*  
> \* \*  
> \* THIS IS NOT A BUG OR A SYSTEM CRASH \*  
> \* \*  
> \* If you did not intend to break into the debugger, press the &#8220;g&#8221; key, then \*  
> \* press the &#8220;Enter&#8221; key now. This message might immediately reappear. If it \*  
> \* does, press &#8220;g&#8221; and &#8220;Enter&#8221; again. \*  
> \* \*  
> \***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\***\****  
> nt!DbgBreakPointWithStatus:  
> fffff800\`026c17a0 cc int 3  
> kd> k  
> Child-SP RetAddr Call Site  
> fffff800\`00b9ca78 fffff800\`026912c3 nt!DbgBreakPointWithStatus  
> fffff800\`00b9ca80 fffff800\`02624090 nt!FsRtlCheckLockForReadAccess+0x1833  
> fffff800\`00b9cb80 00000000\`00000000 0xfffff800\`02624090

おぉ、なんだかとてもそれっぽいことになりましたし、しっかりとTarget Machineに対してBreak-inできたみたいです。超絶に遅いペースですが、一応前進はできています。

<noscript>
</noscript>

 [1]: http://www.dcl.hpi.uni-potsdam.de/research/WRK/2011/01/running-wrk-on-mac-os-with-vmware-fusion/
 [2]: http://kenev.net/wp-content/uploads/2014/04/06f7f14b566d2ea544e754d9f3bdf24b.png
 [3]: http://kenev.net/wp-content/uploads/2014/04/7efc5fcd768886539a64544f8115b06f.png
 [4]: http://kenev.net/wp-content/uploads/2014/04/bab5c9dec7232e164c0ec6ba9edc26a6.png
 [5]: http://kenev.net/wp-content/uploads/2014/04/29461ec9a6e25bca35135dcf1be59552.png
 [6]: http://kenev.net/wp-content/uploads/2014/04/5c481ceef3483ab715c6a039d3fd0835.png
 [7]: http://kenev.net/wp-content/uploads/2014/04/058f868736b038228f5422a3e1549c5c.png
 [8]: http://kenev.net/wp-content/uploads/2014/04/f1dd4298f116f21d0bfbdf6539ec7c13.png