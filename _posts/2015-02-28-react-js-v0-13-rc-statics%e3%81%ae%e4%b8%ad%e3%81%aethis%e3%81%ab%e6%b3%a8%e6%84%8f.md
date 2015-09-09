---
title: 'react.js v0.13 RC &#8211; staticsの中の&#8217;this&#8217;に注意'
author: kenev
layout: post
permalink: /2015/02/28/react-js-v0-13-rc-statics%e3%81%ae%e4%b8%ad%e3%81%aethis%e3%81%ab%e6%b3%a8%e6%84%8f/
categories:
  - JavaScript
  - React.js
  - Web-dev
tags:
  - autobounded
  - call
  - javascript
  - react
  - react.js
  - statics
  - this
  - v0.13rc
  - whats-new
  - 変更点
---
> Static methods (defined in statics) are no longer autobound to the component class 

Reactのコンポーネントには[statics][1]というものがあり、ここで定義したものはコンポーネントから直接呼び出すことができます。サンプルソースは本家のものを参照してください。

staticsで定義されたものは今までは定義したコンポーネントに「自動的」にバインドされていましたが、v0.13rc以降はバインドされなくなります。どういうことかと言うと、&#8217;this&#8217;を使った以下のJSFiddleをみてもらうとわかりやすいと思います。

MyComponentはstaticsにmessageという「文字列」とstaticMethodという「関数」を持っています。staticMethod内で「this.message」を参照しているのですが、ここで「this」が何を指すのかがポイントです。v0.13rcより前はstaticsはコンポーネントに自動的にバインドされていた為、thisはコンポーネント（この場合MyComponent）となります。よって、this.messageはMyComponent.message（つまり&#8217;Hello&#8217;）になるのです。callで明示的にcallerを指定しても、既にMyComponentにバインド済みなので&#8217;Hey!&#8217;ではなく&#8217;Hello&#8217;になります。

これがv0.13rcからは自動的にバインドされなくなるので上記と同じコードを実行した場合は、callで明示的にmessageに&#8217;Hey!&#8217;を持つcallerオブジェクトを指定しているので、出力は&#8217;Hey&#8217;に変わります。

私はまだまだ知識が浅いのでこの変化がどういう効果をもたらすのかよくわかっていないのですが、より柔軟になったのでしょうか？あるいはES6からは「()=>」で定義した関数のthisは元の所有者をキャプチャしてくれるので、React側で機能を制限しない方向にしたのでしょうかね？

 [1]: http://facebook.github.io/react/docs/component-specs.html#statics