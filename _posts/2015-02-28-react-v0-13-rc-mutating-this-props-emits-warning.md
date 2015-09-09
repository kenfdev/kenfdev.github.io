---
title: 'React v0.13 RC &#8211; Mutating this.props emits warning'
author: kenev
layout: post
permalink: /2015/02/28/react-v0-13-rc-mutating-this-props-emits-warning/
categories:
  - react.js
tags:
  - immutable
  - mutate
  - react
  - react.js
  - this.props
  - v0.13rc
---
[React v0.13 RC][1] was recently announced.

Let&#8217;s look at what changed in &#8216;this.props&#8217;

> Mutating props after an element is created is deprecated and will cause warnings in development mode; future versions of React will incorporate performance optimizations assuming that props aren&#8217;t mutated 

Starting from v0.13 RC, mutating this.props will emit a warning in development mode (and only in development mode). Development mode is the unmagnified &#8216;react.js&#8217;.

Nothing really happened when you wrote mutation code on &#8216;this.props&#8217; in previous versions of react.js like below.

In v0.13 RC development mode, a warning will be emitted telling you not to mutate &#8216;this.props&#8217;.

<pre class="lang:default decode:true">Warning: Don't set .props.name of the React component &lt;ExampleApplication /&gt;. Instead, specify the correct value when initially creating the element.</pre>

It just emits a warning but maybe in future release it might even throw an exception. Future releases of react.js will assume that you haven&#8217;t mutated &#8216;this.props&#8217; and will optimize performances related to that condition.

 [1]: http://facebook.github.io/react/blog/2015/02/24/react-v0.13-rc1.html#changelog