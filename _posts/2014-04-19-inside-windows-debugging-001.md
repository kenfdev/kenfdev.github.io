---
title: Inside Windows Debugging 001
author: kenev
layout: post
permalink: /2014/04/19/inside-windows-debugging-001/
categories:
  - 未分類
tags:
  - windbg
---
<span style="line-height: 1.5em;">Recently I&#8217;ve started reading &#8220;Inside Windows Debugging&#8221; to enhance my debugging skills.  I had some trouble in</span>

PART 2 Debugging for Fun and Profit &#8211; Listing Parameters and Locals for Your Own Code

so I&#8217;m going to leave a note how I got through it.

The problem occurred when I tried to set a breakpoint on the &#8220;kernel32!CreateProcessW&#8221;. (below is the command)

> bp kernel32!CreateProcessW

But all I got was a messages indicating some kind of error.

> 0:000> bp kernel32!createprocessw  
> Couldn&#8217;t resolve error at &#8216;kernel32!createprocessw&#8217;

As expected, after I continued with the &#8220;g&#8221; command, the breakpoint didn&#8217;t seem to work.  
Little knowledge did I have but I started by finding the kernel32!CreateProcess functions with the &#8220;x&#8221; command.

> 0:000> x kernel32!CreateProcess*  
> 76b90cb9 KERNEL32!CreateProcessWithTokenW (void)  
> 76b90d84 KERNEL32!CreateProcessAsUserW (void)  
> 76b90d84 KERNEL32!CreateProcessWithLogonW (void)  
> 76b4e225 KERNEL32!CreateProcessWStub = <no type information>  
> 76b72e04 KERNEL32!CreateProcessInternalAStub = <no type information>  
> 76b72e15 KERNEL32!CreateProcessInternalWStub = <no type information>  
> 76b72de2 KERNEL32!CreateProcessAStub = <no type information>  
> 76b72df3 KERNEL32!CreateProcessAsUserWStub = <no type information>

There was no &#8220;kernel32!CreateProcessW&#8221; in the functions list so this was probably the cause of the error above.  Well, I had no clue which function inside of these was the right one to call but luckily I chose &#8220;kernel32!CreateProcessWStub&#8221;.

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
> wchar_t * pwszCommandLine = 0x003571c0 &#8220;notepad.exe&#8221;,  
> <span style="line-height: 1.5em;">unsigned long dwCreationFlags = 0,<br /> </span><span style="line-height: 1.5em;">void ** phProcess = 0x001bfde0,<br /> </span><span style="line-height: 1.5em;">void ** phPrimaryThread = 0x001bfdd8)+0x47 [c:\book\code\chapter_01\workerprocess\main.cpp @ 94]</span>

The breakpoint worked as expected and you could see that the &#8220;notepad&#8221; process was created using the &#8220;kernel32!CreateProcessWStub&#8221;.  Maybe kernel32!CreateProcessW got deprecated in Windows 8.1???  No clue for this moment.

<noscript>
</noscript>