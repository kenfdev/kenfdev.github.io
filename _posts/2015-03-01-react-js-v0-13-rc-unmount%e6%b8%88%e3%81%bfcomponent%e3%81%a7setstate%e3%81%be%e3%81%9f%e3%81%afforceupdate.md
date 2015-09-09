---
title: react.js v0.13 RC – unmount済みComponentでsetStateまたはforceUpdate
author: kenev
layout: post
permalink: /2015/03/01/react-js-v0-13-rc-unmount%e6%b8%88%e3%81%bfcomponent%e3%81%a7setstate%e3%81%be%e3%81%9f%e3%81%afforceupdate/
categories:
  - JavaScript
  - React.js
  - Web-dev
tags:
  - forceUpdate
  - javascript
  - react
  - react.js
  - setState
  - v0.13rc
---
> setState and forceUpdate on an unmounted component now warns instead of throwing. That avoids a possible race condition with Promises. 

アンマウントされたコンポーネントにてsetStateあるいはforceUpdateが呼ばれてしまった場合の挙動がreact.js v0.13rcから変わりました。今までは「例外」がスローされていたのですが、v0.13rc以降からはWarningに留まります。Promiseなどの遅延実行とのレースコンディションを避ける為に歩み寄っていただけたのかもしれません。

事の発端はこの[Issue][1]からで、やはりAJAXなリクエストを発行してから、返事が返ってくるまでの間にコンポーネントがアンマウントされてしまうと例外でアボンしてしまうのどうにかしてくださいってことだったみたいですね。

以下のJSFiddleで従来の挙動は確認可能です。（例外はデバッガで確認可能）JSFiddleの挙動としては、Helloコンポーネントをマウントしてから2秒後にアンマウントさせています。そしてさらにその1秒後にHelloコンポーネント内でsetStateを呼び出しています。

<pre class="lang:default decode:true  "># react.js &lt; v0.13rc
Uncaught Error: Invariant Violation: replaceState(...): Can only update a mounted or mounting component.

# v0.13rc &lt;= react.js
Warning: setState(...): Can only update a mounted or mounting component. This usually means you called setState() on an unmounted component. This is a no-op.</pre>

&nbsp;

 [1]: https://github.com/facebook/react/issues/1247