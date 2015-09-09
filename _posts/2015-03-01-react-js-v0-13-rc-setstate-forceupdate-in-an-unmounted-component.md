---
title: react.js v0.13 RC – setState, forceUpdate in an unmounted Component
author: kenev
layout: post
permalink: /2015/03/01/react-js-v0-13-rc-setstate-forceupdate-in-an-unmounted-component/
categories:
  - react.js
tags:
  - changes
  - forceUpdate
  - react
  - react.js
  - setState
  - unmounted
  - v0.13rc
---
> setState and forceUpdate on an unmounted component now warns instead of throwing. That avoids a possible race condition with Promises. 

Prior to v0.13rc, calling setState, forceUpdate in an unmounted component threw an exception but starting from v0.13rc, warnings will only be emitted. This is related to an [Issue on Github][1] which people mention Promises (especially slow ones) resolving after a component has been unmounted.

The JSFiddle below is an example of a setState being called inside the Hello component after it has been unmounted.

<pre class="lang:default decode:true  "># react.js &lt; v0.13rc
Uncaught Error: Invariant Violation: replaceState(...): Can only update a mounted or mounting component.

# v0.13rc &lt;= react.js
Warning: setState(...): Can only update a mounted or mounting component. This usually means you called setState() on an unmounted component. This is a no-op.</pre>

&nbsp;

 [1]: https://github.com/facebook/react/issues/1247