---
title: Inside Windows Debugging その１
author: kenev
layout: post
permalink: /2014/04/19/inside-windows-debugging-%e3%81%9d%e3%81%ae%ef%bc%91/
categories:
  - Windows
tags:
  - windbg
---
最近はWindowsのデバッグ能力もワンステップ上を目指したいという気持ちから「Inside Windows Debuggin」を読み始めました。開始して間もなくつまってしまったのでメモ。

PART 2 Debugging for Fun and Profit &#8211; Listing Parameters and Locals for Your Own Code

という章でNotepad起動時にブレークポイントを貼る以下のコマンドがあります。

> bp kernel32!CreateProcessW

が、しかしこれを実行すると返ってくるのは

> 0:000> bp kernel32!createprocessw  
> Couldn&#8217;t resolve error at &#8216;kernel32!createprocessw&#8217;

最初は気にせずGOしたのですが、どう考えてもブレークしてくれてなさそうだったので調査。  
まず、kernel32!CreateProcess系の関数をリストアップしてみると以下のようになった。

> 0:000> x kernel32!CreateProcess*  
> 76b90cb9 KERNEL32!CreateProcessWithTokenW (void)  
> 76b90d84 KERNEL32!CreateProcessAsUserW (void)  
> 76b90d84 KERNEL32!CreateProcessWithLogonW (void)  
> 76b4e225 KERNEL32!CreateProcessWStub = <no type information>  
> 76b72e04 KERNEL32!CreateProcessInternalAStub = <no type information>  
> 76b72e15 KERNEL32!CreateProcessInternalWStub = <no type information>  
> 76b72de2 KERNEL32!CreateProcessAStub = <no type information>  
> 76b72df3 KERNEL32!CreateProcessAsUserWStub = <no type information>

この中に「kernel32!CreateProcessW」がリストアップされてないことが原因？と思ったんだけど、じゃあどれにブレークポイント貼ればいいの？ってなってとりあえず適当に貼ってみました。なんとなくそれっぽい「kernel32!createprocesswstub」を選択。

> 0:000> bp kernel32!createprocesswstub  
> 0:000> g  
> ModLoad: 76ee0000 76f05000 C:\WINDOWS\SysWOW64\IMM32.DLL  
> ModLoad: 74ca0000 74d97000 C:\WINDOWS\SysWOW64\MSCTF.dll  
> Breakpoint 0 hit  
> eax=001bfcec ebx=00000000 ecx=00000000 edx=00000000 esi=00000000 edi=001bfd40  
> eip=76b4e225 esp=001bfcb8 ebp=001bfd40 iopl=0 nv up ei pl nz ac pe nc  
> cs=0023 ss=002b ds=002b es=002b fs=0053 gs=002b efl=00000216  
> KERNEL32!CreateProcessWStub:  
> 76b4e225 8bff mov edi,edi  
> 0:000> kP  
> ChildEBP RetAddr  
> 001bfcb4 002a1732 KERNEL32!CreateProcessWStub

いい感じに止まってくれました。Windows8.1でCreateProcessWが無くなったのかな？？？

<noscript>
</noscript>